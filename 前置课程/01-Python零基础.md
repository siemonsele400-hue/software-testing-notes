# 前置课程 01：Python 零基础（面向软件测试）

> 定位：完全没有编程基础，也能用本章完成数据处理、接口调用和 Pytest 测试脚本。

## 1. 学习目标与适用岗位

- 看懂变量、条件、循环、函数、列表、字典和异常处理。
- 能独立写一个可重复运行的小脚本，而不是只会复制代码。
- 能为 Requests、Pytest、Selenium 自动化打下语法基础。
- 适合初级功能测试、接口测试、自动化测试岗位。

## 2. 环境与第一个程序

安装 Python 3，并在 PowerShell 验证：

```powershell
python --version
python -m pip --version
```

创建 `hello_test.py`：

```python
name = "登录接口"
print(f"开始测试：{name}")
```

运行：`python hello_test.py`。如果提示找不到命令，检查 PATH、重新打开终端，或使用 `py hello_test.py`。

## 3. 核心语法

### 3.1 变量和类型

变量是给数据绑定的名字。常见类型：`int` 整数、`float` 小数、`str` 字符串、`bool` 布尔值、`None` 空值。测试中要特别区分字符串 `"200"` 和数字 `200`。

```python
status_code = 200
body_text = "success"
passed = status_code == 200
print(type(status_code), passed)
```

### 3.2 条件与循环

```python
status = 200
if status == 200:
    result = "通过"
elif status >= 500:
    result = "服务端错误"
else:
    result = "需要分析"

for case_id in ["login_001", "login_002"]:
    print(case_id, result)
```

常见错误是缩进不一致、把 `=` 当成比较、循环中修改正在遍历的列表。优先使用 4 个空格，并让变量名表达业务含义。

### 3.3 字符串处理

```python
username = "  alice@example.com "
clean = username.strip().lower()
assert "@" in clean
```

常用方法：`strip` 去空格、`split` 分割、`join` 拼接、`replace` 替换、`startswith`/`endswith` 判断前后缀。断言前先处理编码和空值，避免把格式问题误判为业务 Bug。

### 3.4 列表、字典与 JSON 思维

列表适合有顺序的多条数据，字典适合“字段名:字段值”。接口响应通常可以转换为嵌套字典和列表。

```python
users = [{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}]
for user in users:
    if user["id"] == 2:
        assert user["name"] == "Bob"

case = {"method": "POST", "path": "/api/login", "expected": 200}
assert case.get("expected") == 200
```

使用 `get` 可以处理缺失字段；直接写 `case["token"]` 在字段不存在时会抛 `KeyError`，应根据测试目的决定是失败还是给默认值。

## 4. 函数、模块和复用

```python
def is_success(status_code: int, expected: int = 200) -> bool:
    return status_code == expected

def build_headers(token: str | None = None) -> dict:
    headers = {"Content-Type": "application/json"}
    if token:
        headers["Authorization"] = f"Bearer {token}"
    return headers
```

函数应做到输入明确、输出明确、职责单一。把重复的登录、生成数据、校验响应逻辑放入函数，后续才能封装成测试框架。

## 5. 异常、文件和日志

```python
from pathlib import Path

try:
    value = int("abc")
except ValueError as exc:
    print(f"测试数据非法：{exc}")

Path("result.txt").write_text("login passed\n", encoding="utf-8")
```

不要使用裸 `except:` 吞掉所有错误。记录异常类型、输入数据和上下文；测试失败时，日志比一句“执行失败”更有价值。

## 6. 虚拟环境与依赖

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install requests pytest
python -m pip freeze > requirements.txt
```

虚拟环境解决不同项目依赖冲突。不要把 `.venv` 提交到 Git；提交 `requirements.txt`，让同事能够重建环境。

## 7. 面向测试的完整小练习

```python
def validate_login_response(response: dict) -> list[str]:
    errors = []
    if response.get("code") != 0:
        errors.append("业务码不为0")
    if not response.get("token"):
        errors.append("缺少token")
    if response.get("user", {}).get("id") is None:
        errors.append("缺少用户ID")
    return errors

assert validate_login_response({"code": 0, "token": "t", "user": {"id": 1}}) == []
```

## 8. 常见排查

| 现象 | 排查顺序 |
|---|---|
| `SyntaxError` | 看报错行前后的括号、引号、冒号和缩进 |
| `NameError` | 检查变量是否先定义、拼写和作用域 |
| `TypeError` | 打印 `type()`，确认字符串与数字是否混用 |
| `KeyError` | 打印字典键，必要时使用 `get` |
| `ModuleNotFoundError` | 确认已激活虚拟环境并用当前 Python 安装包 |

## 9. 练习与答案

**练习 1：** 写函数，判断用户名长度是否在 6～20 个字符内。

**答案：** `def valid_name(s): return 6 <= len(s.strip()) <= 20`。解析：先去除首尾空格，再按业务规则判断；若系统按字节限制，还要额外处理中文编码。

**练习 2：** 从响应字典中安全读取 `data.items`，缺失时返回空列表。

**答案：** `items = response.get("data", {}).get("items", [])`。解析：逐层提供默认字典，避免空值导致异常。

## 10. 面试表达

问：测试人员为什么学 Python？

答：我用 Python 处理测试数据、调用接口、组织 Pytest 用例、生成报告和接入 CI。重点不是写业务系统，而是减少重复执行、提高覆盖和让结果可追溯。

## 11. 课后输出

完成一个“登录响应校验器”，包含成功、业务失败、字段缺失、类型错误四类数据，并把代码、运行结果和 README 提交到 Git。
