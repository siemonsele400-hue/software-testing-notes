# 第 15 集：Dos 命令详情

> **视频对应**：视频演示 `cd`、`dir`、`findstr`、`ipconfig`、`ping`、`nslookup`、`netstat`、`tasklist`、`taskkill`，用于检查文件、日志、进程、端口和网络。

## 1. 测试场景

```bat
cd /d D:\project\logs
dir /a /o:-d
findstr /s /i /n "ERROR timeout requestId" *.log
ipconfig /all
ping api.test.local
nslookup api.test.local
netstat -ano | findstr ":8080"
tasklist /fi "PID eq 1234"
```

`ping` 只说明 ICMP 可达，不能证明 HTTP 正常；`nslookup` 查 DNS；`netstat -ano` 把端口和 PID 对应；`tasklist` 查进程。`taskkill /PID <pid> /T /F` 会强制结束进程，必须确认 PID、归属和授权，优先正常停止并留记录。

## 2. 分层排查

接口失败时先确认日志目录和版本，再查 DNS、基础连通、端口监听、服务进程，最后用 `curl` 或 Postman 验证 HTTP。每层保存命令输出，避免把网络不通、服务未启动和接口 500 混为一谈。

## 3. 练习题与答案解析

### 练习 1

**题目**：打不开 `api.test.local:8080`，给出排查命令。

**参考答案**：`nslookup` 查解析；`ping` 查基础连通；`netstat -ano | findstr ":8080"` 查监听；`tasklist` 查进程；`curl -I http://api.test.local:8080/health` 查 HTTP。

**解析**：每条命令只证明一层。

**验收标准**：覆盖 DNS、连通性、端口、进程和 HTTP。

### 练习 2

**题目**：如何快速找最近错误日志？

**参考答案**：`dir /o:-d *.log` 找最新文件，再 `findstr /i /n "ERROR Exception timeout" app-*.log`，记录文件名、时间和 request ID。

**解析**：先缩小范围再检索，结果更易与测试时间对应。

## 4. 面试问法与课后任务

**问：ping 通但接口失败可能为何？** 端口、进程、TLS、代理、防火墙或应用 4xx/5xx。

**任务**：在授权本机服务上记录目录、日志、端口、进程排查链路，不在生产机强杀进程。
