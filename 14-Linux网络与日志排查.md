# 14 Linux、网络与日志排查

## 1. 学习目标

测试人员不一定负责运维，但必须能判断问题属于客户端、网络、服务进程、数据库还是业务代码，并提供开发可用的证据。

## 2. Linux 文件和权限

```bash
pwd
ls -lah
cd /var/log
mkdir -p qa-data
cp source.log qa-data/
mv old.log archive.log
rm -i archive.log
```

权限：

```bash
whoami
id
ls -l app.log
chmod 640 app.log
chown qa:qa app.log
```

生产环境不要为了排查随意 `chmod 777` 或使用 root 执行未知脚本。

## 3. 进程、端口和服务

```bash
ps -ef | grep java
top
free -h
df -h
ss -lntp
curl -I http://127.0.0.1:8080/health
```

排查“接口无法访问”时依次确认：进程是否存在、端口是否监听、端口是否能从客户端到达、HTTP 是否返回、日志是否有请求记录。

## 4. 日志检索

```bash
tail -f app.log
grep -n "ERROR" app.log
grep -n "request-id-123" app.log
awk '{print $1,$2,$NF}' app.log | head
```

日志证据应保留时间、请求 ID、用户/业务 ID、异常栈、下游响应和线程信息。上传到 Bug 前脱敏 token、密码、手机号和身份证。

## 5. 网络分层排查

### DNS

```bash
nslookup api.test.example.com
dig api.test.example.com
```

### 连通性与端口

```bash
ping api.test.example.com
nc -vz api.test.example.com 443
```

### HTTP

```bash
curl -v https://api.test.example.com/health
curl -X POST https://api.test.example.com/login \
  -H 'Content-Type: application/json' \
  -d '{"username":"demo","password":"secret"}'
```

`ping` 成功只说明 ICMP 层可能可达，不代表 TCP 端口、TLS 或 HTTP 业务正常。

## 6. HTTP 问题定位

- 连接超时：网络、路由、防火墙、服务未监听或连接池。
- TLS 错误：证书过期、主机名不匹配、协议版本或代理问题。
- 401/403：认证失效、权限不足、签名错误或网关策略。
- 404：路径、版本、网关路由或资源不存在。
- 429：限流、重试风暴或压测超过阈值。
- 500：服务端异常，需结合请求 ID 和服务日志。
- 502/503/504：网关与上游服务、健康检查、超时或容量问题。

## 7. 日志与 Bug 证据模板

```text
发生时间（含时区）：
环境/版本：
请求 ID：
用户/业务 ID（脱敏）：
客户端现象：
HTTP 请求与响应：
应用日志摘要：
数据库/下游证据：
影响范围：
复现频率：
```

## 8. 常见排查误区

- 看到 500 就认定是后端代码，没有先确认请求参数和环境。
- 看到页面空白就重启服务，导致原始日志和现场丢失。
- 只截一张图，没有时间、版本和请求 ID。
- 把完整生产日志上传到公共聊天或仓库。
- 把一次偶发网络抖动直接写成确定性产品缺陷。

## 9. 练习与答案解析

### 练习 1：端口

**问题**：域名能解析、服务器能 ping 通，但 `curl` 连接 8080 超时，下一步查什么？

**答案**：确认服务进程和 8080 监听状态，再检查防火墙、安全组、代理和路由；如果端口监听但外部超时，重点看网络策略。

**解析**：从 DNS 到 ICMP 到 TCP 逐层缩小范围。

### 练习 2：日志关联

**问题**：如何让开发快速定位一条偶发接口失败？

**答案**：提供精确时间、环境、请求 ID、脱敏参数、响应状态、业务 ID、复现频率和对应日志片段。

**解析**：请求 ID 比模糊描述“刚刚失败过”更有定位价值。

### 练习 3：命令安全

**问题**：为什么不建议测试人员随意执行 `rm -rf` 或 `chmod 777`？

**答案**：可能造成数据删除、权限扩大和安全事故；应确认路径、权限和回滚方案，使用最小必要权限。

**解析**：排查效率不能凌驾于环境和数据安全。

## 10. 动手任务

- 在本地启动一个 HTTP 服务，完成 DNS、端口、HTTP 三层排查。
- 从一份示例日志中提取 ERROR、请求 ID 和响应耗时。
- 写一份包含证据和脱敏处理的网络类 Bug。
