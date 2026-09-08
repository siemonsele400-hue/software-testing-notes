# 10 Web 自动化测试

## 1. 视频覆盖

- 第 48～58 集：自动化简介、环境搭建与元素定位、项目实战、POM、断言、企业框架、DDT/Excel、Pytest 和 Jenkins。

标注：课程原讲（Selenium、定位和 POM）；基础补充（等待、同步和可维护性）；企业实战（截图、隔离和无头执行）；就业重点（自动化边界与排查）。

## 2. 学习目标

- 能判断哪些 Web 场景适合自动化。
- 能稳定定位元素、完成浏览器交互并验证结果。
- 能用 POM、数据驱动和 Pytest 构建可维护脚本。
- 能处理等待、弹窗、窗口、iframe、文件和失败截图。

## 3. 自动化测试边界

适合自动化：稳定、重复、高频、规则明确、回归成本高的核心流程。

不宜优先自动化：频繁改版、强视觉判断、一次性活动、需要大量人工探索的场景。

自动化不是越多越好，应以风险、维护成本和反馈速度衡量价值。

## 4. Selenium 核心对象

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Chrome()
driver.get("https://test.example.com/login")
WebDriverWait(driver, 10).until(
    EC.visibility_of_element_located((By.ID, "username"))
).send_keys("demo")
```

测试结束必须关闭浏览器；更完整的工程应在 fixture 中统一创建和清理 driver。

## 5. 元素定位

常见定位方式：ID、Name、Class、Tag、Link Text、CSS Selector、XPath。

优先级建议：稳定唯一的 ID 或业务属性 > CSS > 简洁 XPath。不要依赖易变的层级、随机 class、中文显示文本或绝对 XPath。

定位策略应与开发约定稳定的 `data-testid` 等测试属性，减少页面样式变化对脚本的影响。

## 6. 等待与同步

- 隐式等待：全局等待，简单但容易掩盖真正的同步问题。
- 显式等待：等待某个条件，推荐用于关键步骤。
- 固定 `sleep`：只适合临时调试，不应作为主要同步方案。

常见条件：元素存在、可见、可点击、文本出现、URL 变化、页面加载完成。

错误做法是“每一步 sleep 5 秒”，这会让脚本慢且仍然不稳定。

## 7. 常见交互

- 下拉框：原生 Select 或自定义下拉列表。
- 弹窗：alert/confirm/prompt 与页面内 modal 要区分。
- 多窗口：保存原窗口句柄，切换到目标窗口后再切回。
- iframe：先切入 frame，操作结束切回 default content。
- 文件上传：使用 `send_keys` 输入文件路径；下载需校验文件存在、大小和内容。
- 滚动、悬停、拖拽、键盘操作：优先使用稳定的 Selenium API。

## 8. POM 设计模式

页面对象负责元素和页面动作，测试用例负责业务场景和断言。

```python
class LoginPage:
    def __init__(self, driver):
        self.driver = driver

    def login(self, username, password):
        self.driver.find_element(By.ID, "username").send_keys(username)
        self.driver.find_element(By.ID, "password").send_keys(password)
        self.driver.find_element(By.ID, "submit").click()
```

不要让 Page Object 隐藏所有断言；页面对象可以返回页面状态，业务测试决定期望结果。

## 9. 断言与证据

验证 URL、标题、文本、元素状态、列表、数据库和下载文件。失败时自动保存截图、页面源码、浏览器日志和关键变量。

避免只断言“元素存在”，要验证它是否可见、可用、内容正确和状态变化符合业务。

## 10. 数据驱动

可用 Pytest 参数化、Excel、CSV、JSON 或 YAML。数据应包含成功、格式错误、空值、边界和权限场景。Excel 适合业务人员维护，但复杂结构和版本管理通常更适合 YAML/JSON。

## 11. Pytest 与 Jenkins

- 使用 fixture 管理浏览器和前后置。
- 使用 marker 分组 smoke/regression。
- 失败重试需谨慎，避免掩盖真实问题。
- Jenkins 中无头模式执行，归档 Allure、截图和日志。
- 并行执行前确认数据、账号和环境支持隔离。

## 12. 常见错误与排查

- 元素找不到：检查页面是否切换、iframe、窗口、定位器和等待条件。
- 点击无效：元素被遮挡、未可点击、页面仍在加载或需要滚动。
- 脚本偶发失败：固定等待不足、共享数据、环境不稳定或定位器脆弱。
- 用例相互影响：没有清理 cookies、数据库、下载文件或业务数据。
- 无头模式异常：窗口大小、权限、字体、下载路径和浏览器版本差异。

## 13. 练习与答案解析

### 练习 1：定位器

**问题**：页面 class 每次构建都会变化，应该如何定位登录按钮？

**答案**：优先与开发约定稳定的 `id` 或 `data-testid`；其次使用稳定的 CSS 属性组合，避免随机 class 和绝对 XPath。

**解析**：定位器的核心是长期稳定，而不是当前能找到。

### 练习 2：等待

**问题**：为什么不建议大量使用 `sleep(5)`？

**答案**：会固定浪费时间，且无法保证页面在 5 秒内完成；应使用显式等待等待具体条件。

**解析**：同步应描述“等待什么”，而不是猜“等待多久”。

### 练习 3：POM

**问题**：POM 中页面对象和测试用例分别负责什么？

**答案**：页面对象封装元素定位和页面动作，测试用例编排业务流程并验证结果。

**解析**：职责分离能降低页面改版时的维护成本。

## 14. 面试高频问法

### 问：Web 自动化失败如何定位？

先看截图、日志、页面源码和失败步骤，再确认环境、窗口/iframe、元素定位、等待、数据和业务状态；必要时用手工步骤复现，判断是脚本问题、产品问题还是环境问题。

## 15. 动手任务

- 自动化登录、搜索、下单和退出流程。
- 用 POM 重构脚本，加入显式等待和失败截图。
- 用参数化覆盖有效、空值和边界数据。
- 配置 Jenkins 无头执行并归档报告。
