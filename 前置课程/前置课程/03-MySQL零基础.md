# 前置课程 03：MySQL 零基础（测试实战）

## 1. 学习目标

能理解表、行、列、主键和外键；完成增删改查、多表查询、聚合和事务验证；能用 SQL 为接口测试准备数据、核对结果和定位 Bug。

## 2. 数据库基本概念

数据库保存结构化数据，表由行和列组成。主键唯一标识一行；外键表达表之间的关联；索引加速查询但会增加写入和存储成本。测试人员不应只会“查到数据”，还要确认状态、金额、时间和关联记录符合业务规则。

## 3. 建库建表与测试数据

```sql
CREATE DATABASE test_shop CHARACTER SET utf8mb4;
USE test_shop;
CREATE TABLE users (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(50) NOT NULL UNIQUE,
  status TINYINT NOT NULL DEFAULT 1,
  created_at DATETIME NOT NULL
);
INSERT INTO users(username, status, created_at)
VALUES ('alice', 1, NOW()), ('disabled', 0, NOW());
```

测试数据要可重复：使用明确前缀、唯一编号和清理脚本；不要直接修改共享环境中的真实数据。敏感数据应脱敏，金额和数量使用合适的精度类型而不是浮点数。

## 4. 查询与筛选

```sql
SELECT id, username FROM users WHERE status = 1 ORDER BY id DESC LIMIT 20;
SELECT COUNT(*) AS active_count FROM users WHERE status = 1;
SELECT username FROM users WHERE username LIKE 'ali%';
```

`WHERE` 过滤行，`ORDER BY` 排序，`LIMIT` 分页。没有 `ORDER BY` 的分页结果顺序不稳定；`NULL` 不能用 `= NULL` 判断，应使用 `IS NULL`。

## 5. 更新、删除与事务

```sql
START TRANSACTION;
UPDATE users SET status = 0 WHERE username = 'alice';
SELECT ROW_COUNT();
ROLLBACK;
```

先用同样条件执行 `SELECT`，确认影响范围，再执行 `UPDATE`/`DELETE`。生产操作必须经过审批和备份。事务的原子性要求一组操作要么全部成功，要么全部回滚；测试应验证中途失败是否留下半成品。

## 6. 多表查询

```sql
SELECT o.id, u.username, o.amount
FROM orders o
JOIN users u ON u.id = o.user_id
WHERE o.status = 'PAID';
```

`INNER JOIN` 只返回匹配行，`LEFT JOIN` 保留左表全部记录，适合找“没有订单的用户”。连接条件写错会产生笛卡尔积，表现为数量异常膨胀。

## 7. NULL、时间和金额陷阱

- `COUNT(*)` 统计行数，`COUNT(column)` 不统计该列为 `NULL` 的行。
- 时间比较要明确时区、边界是否包含，避免把 `2026-09-09 00:00:00` 漏掉。
- 金额比较应按最小货币单位或 `DECIMAL`；不要依赖浮点相等。
- 字符串排序、大小写和字符集会影响结果，中文环境使用 `utf8mb4`。

## 8. 用 SQL 验证接口

接口创建订单后，可按订单号查询订单、订单明细、库存流水和支付流水；核对状态流转、总金额、数量和唯一性。验证失败时记录 SQL、执行时间、环境、事务状态和关联请求 ID。

## 9. 性能与安全基础

用 `EXPLAIN` 查看查询计划，关注是否走索引和扫描行数。接口参数必须使用参数化查询，禁止把用户输入拼接到 SQL 中。测试 SQL 注入时只在授权环境执行，并记录现象而不是破坏数据。

## 10. 练习与答案

**练习 1：** 找出注册超过 30 天但仍未激活的用户。

**答案：** `SELECT * FROM users WHERE status=0 AND created_at < DATE_SUB(NOW(), INTERVAL 30 DAY);`。解析：时间条件应与业务定义的“超过”一致，并关注时区。

**练习 2：** 如何验证支付失败没有扣库存？

**答案：** 根据订单号分别查询支付状态、库存主表和库存流水，比较失败前后数量；同时检查事务回滚日志和重复请求结果。

## 11. 面试表达

问：测试人员常用哪些 SQL？

答：我常用筛选、排序、聚合、关联和子查询准备与核对数据，并用事务保护测试环境；涉及复杂问题会结合执行计划、日志和请求 ID 定位，而不是只截一条查询结果。

## 12. 课后输出

为登录、下单、支付三个接口各写一份“前置数据 SQL + 验证 SQL + 清理 SQL”，并说明事务边界。
