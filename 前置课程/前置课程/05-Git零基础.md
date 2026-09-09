# 前置课程 05：Git 零基础（测试资产协作）

## 1. 学习目标

能用 Git 管理测试脚本、用例、数据模板和文档；理解工作区、暂存区、提交、分支、远程仓库和冲突；做到提交可审查、问题可回溯。

## 2. 基本模型

工作区是正在编辑的文件；`git add` 把变更放入暂存区；`git commit` 创建本地历史快照；`git push` 才上传远程仓库。Git 不等于 GitHub，离线也可以使用 Git。

## 3. 首次配置与初始化

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git init
git status
```

项目应提供 `.gitignore`，排除 `.venv/`、缓存、测试报告临时文件、密钥和本地配置。任何 Token、密码、Cookie 都不应提交；加入 `.gitignore` 不能清除已经进入历史的秘密。

## 4. 日常提交

```powershell
git status --short
git diff
git add 前置课程/01-Python零基础.md
git diff --staged
git commit -m "docs: 补充 Python 测试基础"
```

提交前查看 diff，确保没有无关文件、隐私和调试垃圾。一个提交只表达一个逻辑变化，消息说明“做了什么”。

## 5. 分支与合并

```powershell
git switch -c codex/add-api-tests
git switch main
git merge codex/add-api-tests
```

分支让开发和测试资产互不干扰。测试人员可为新接口创建分支，提交测试用例和自动化脚本，通过 Pull Request 评审后合并。

## 6. 远程协作

```powershell
git remote -v
git fetch origin
git pull --ff-only
git push -u origin codex/add-api-tests
```

`fetch` 下载远程历史但不修改当前分支；`pull` 会整合远程内容。执行前先保存或提交本地变更，并查看团队分支策略。

## 7. 冲突处理

冲突文件包含 `<<<<<<<`、`=======`、`>>>>>>>` 标记。先理解双方修改，手工整理成最终内容，执行验证，再 `git add` 和 `git commit`。不要未经理解就保留一边，更不要用破坏性命令覆盖同事工作。

## 8. 测试项目建议结构

```text
tests/
  api/
  web/
testdata/
docs/
reports/       # 通常只保留必要样例
requirements.txt
README.md
.gitignore
```

README 至少写环境、安装、运行命令、数据要求和报告位置，让别人能复现。

## 9. 常见问题

| 问题 | 处理 |
|---|---|
| 提交了不该提交的本地文件 | 先停止推送，确认是否含秘密，再从跟踪中移除并按团队流程处理历史 |
| push 被拒绝 | `fetch` 查看分支差异，整合远程更新后重试 |
| 不知道改了什么 | `git status`、`git diff`、`git log --oneline` |
| 冲突不会选 | 找到业务所有者确认，合并后运行测试 |

## 10. 练习与答案

**练习 1：** 修改了三份文件，但本次只想提交一份怎么办？

**答案：** 仅对目标文件执行 `git add 文件名`，用 `git diff --staged` 检查后提交；其余改动继续留在工作区。

**练习 2：** 为什么不能提交自动化账号密码？

**答案：** Git 历史会长期保存，仓库权限变化或日志泄漏都可能暴露凭证。应使用环境变量、密钥管理或 CI Credentials，并及时轮换意外泄露的凭证。

## 11. 面试表达

问：测试如何参与代码评审？

答：我会评审测试覆盖、边界、可测性、日志和配置变化；自动化代码按分支和 PR 提交，提交前运行用例并附报告，让每个变更可审查和回溯。

## 12. 课后输出

建立一个测试练习仓库，至少完成三个独立提交：项目说明、测试用例、自动化脚本；创建分支修改一条用例，再合并回来。
