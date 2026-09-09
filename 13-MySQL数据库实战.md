# 13 MySQL 数据库实战

## 1. 视频覆盖与定位

本章对应课程资料中的数据库专题和第 27～33 节相关内容，补充测试岗位真正需要的 SQL、数据校验和问题定位能力。

## 2. 数据库基础模型

- 数据库：存放一组相关数据的容器。
- 表：按行和列组织数据。
- 主键：唯一标识一行，不能重复，通常不允许为空。
- 外键：表达表之间的关联；实际项目也可能通过应用层维护关联。
- 唯一约束：防止业务字段重复。
- 非空约束：保证必填字段有值。
- 索引：加速查询，但会增加写入和存储成本。
- 事务：一组操作要么全部成功，要么全部回滚。

## 3. CRUD 基础

```sql
CREATE DATABASE test_shop DEFAULT CHARACTER SET utf8mb4;
USE test_shop;

CREATE TABLE users (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(64) NOT NULL UNIQUE,
  status TINYINT NOT NULL DEFAULT 1,
  created_at DATETIME NOT NULL
);

INSERT INTO users(username, created_at) VALUES ('demo01', NOW());
SELECT id, username, status FROM users WHERE username = 'demo01';
UPDATE users SET status = 0 WHERE username = 'demo01';
DELETE FROM users WHERE username = 'demo01';
```

测试环境执行 `UPDATE`/`DELETE` 前先用同样条件 `SELECT`，并尽量在事务中验证和回滚。

## 4. 查询核心

```sql
SELECT u.username, COUNT(o.id) AS order_count
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
WHERE u.created_at >= '2026-01-01'
GROUP BY u.id, u.username
HAVING COUNT(o.id) >= 1
ORDER BY order_count DESC
LIMIT 20 OFFSET 0;
```

执行顺序可理解为：`FROM/JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT`。这有助于解释为什么聚合条件应放在 `HAVING`，而不是 `WHERE`。

## 5. JOIN 与空值

- `INNER JOIN`：只保留两表都匹配的数据。
- `LEFT JOIN`：保留左表全部数据，右表无匹配时为 `NULL`。
- `RIGHT JOIN`：语义上等价于调换左右表，实际项目较少使用。
- `CROSS JOIN`：笛卡尔积，除非明确需要，否则容易造成数据爆炸。

`NULL` 不是空字符串也不是 0，不能写 `= NULL`，应使用 `IS NULL` 或 `IS NOT NULL`。

## 6. 测试岗位常用校验

### 注册

- 用户名是否唯一。
- 密码是否哈希存储，不能明文保存。
- 创建时间、状态和默认角色是否正确。
- 注册失败时是否产生脏数据。

### 下单

- 订单、订单明细、库存和支付记录是否关联正确。
- 金额汇总是否等于明细合计、优惠和运费规则。
- 重复提交是否创建重复订单。
- 失败事务是否回滚。

### 退款

- 原订单、退款单和账务流水状态是否一致。
- 重复退款是否被拒绝。
- 退款金额是否不超过可退金额。

## 7. 事务与隔离

```sql
START TRANSACTION;
UPDATE inventory SET stock = stock - 1
WHERE sku_id = 1001 AND stock > 0;
-- 检查影响行数、订单和流水
COMMIT;
-- 发生异常时使用 ROLLBACK;
```

测试要关注脏读、不可重复读、幻读、锁等待和死锁。不要只看最终页面，要结合事务边界和并发行为判断数据是否正确。

## 8. 索引与慢查询基础

- 过滤、关联、排序字段可能需要索引，但要根据真实查询和数据分布决定。
- 对列使用函数、隐式类型转换或前导 `%` 可能导致索引失效。
- 使用 `EXPLAIN` 观察访问类型、候选索引、实际选择和扫描行数。

```sql
EXPLAIN SELECT * FROM orders WHERE user_id = 1001 AND status = 'PAID';
```

测试人员不必替开发设计所有索引，但应能提供慢 SQL、数据量、执行计划和复现条件。

## 9. 数据准备与清理

- 测试数据使用明确前缀，如 `qa_20260909_`。
- 记录生成脚本和清理脚本。
- 不依赖某条用例执行后留下的随机状态。
- 并发测试使用独立账号和数据范围。
- 脱敏手机号、邮箱、证件号和银行卡号。

## 10. 练习与答案解析

### 练习 1：重复用户名

**问题**：如何验证注册接口不会创建重复用户名？

**答案**：先用未注册用户名注册成功，再使用相同用户名重复提交；验证第二次返回业务错误、数据库仍只有一条用户记录，并检查并发重复请求下唯一约束或幂等逻辑。

**解析**：只验证页面提示不够，必须校验数据库数量和并发行为。

### 练习 2：LEFT JOIN

**问题**：想查询所有用户及其订单数，即使没有订单的用户也要显示，应使用什么连接？

**答案**：以用户表为左表使用 `LEFT JOIN`，并用 `COUNT(order.id)` 统计；没有订单的用户应显示 0。

**解析**：`INNER JOIN` 会丢失无订单用户。

### 练习 3：安全

**问题**：测试数据库中保存了真实身份证和银行卡号，应该怎么处理？

**答案**：立即停止传播，按组织流程脱敏或清理，限制访问，检查备份和日志，并改用合成数据。

**解析**：测试数据安全属于质量和合规风险，不能因为“只是测试环境”而忽略。

## 11. 面试高频问法

### 问：接口返回成功后，你如何验证数据库？

根据业务主键或请求 ID 查询相关表，检查记录数量、字段、状态、金额、时间、关联关系和事务结果；异步场景使用轮询或最终一致性窗口，并记录查询 SQL 和环境。

## 12. 动手任务

- 建立用户、商品、订单、订单明细四张表。
- 为注册、下单、退款各写 10 条 SQL 校验。
- 用 `EXPLAIN` 比较有无索引时的查询计划。
