# 阶段二：Linux 和数据库

## 第二章：Linux 常用基础命令

本章围绕 Linux 日常文件和目录操作展开，重点掌握 `ls`、`cd`、`touch`、`mkdir`、`rm`、`cp`、`mv`、`cat`、`more`、`grep`、重定向和管道符。第一章已经介绍 Linux、文件系统、远程连接和命令格式，本章进一步把这些命令练到能够独立完成文件管理和基础排查。

本章对应课程视频目录“第二章 Linux 常用基础命令”，包括目录查看、目录切换、文件及目录操作、查看文件内容、重定向、管道符和章节复习。

---

# 2-1 学习目标

完成本章后，应能够：

1. 使用 `ls` 查看文件和目录的详细信息；
2. 使用通配符筛选符合条件的文件；
3. 使用 `cd` 在绝对路径和相对路径之间切换；
4. 使用 `touch`、`mkdir` 创建文件和目录；
5. 使用 `rm`、`cp`、`mv` 删除、复制、移动和重命名文件或目录；
6. 使用 `cat`、`more`、`grep` 查看和筛选文件内容；
7. 使用 `>`、`>>` 保存命令结果；
8. 使用 `|` 将多个命令组合起来；
9. 在临时目录中完成一组可复现的 Linux 文件操作练习。

---

# 2-2 查看目录内容：`ls`

## 2-2.1 基本格式

```shell
ls [选项] [文件或目录]
```

常用示例：

```shell
ls
ls /etc
ls -l /var/log
ls -lah /home
```

## 2-2.2 常用选项

| 选项 | 含义 |
|---|---|
| `-l` | 使用详细列表显示 |
| `-a` | 显示所有文件，包括隐藏文件 |
| `-h` | 文件大小使用更易读的单位显示 |

常用组合：

```shell
ls -l
ls -la
ls -lh
ls -lah
```

## 2-2.3 `ls -l` 输出结构

示例：

```text
-rw-r--r-- 1 root root 1234 Aug  6 10:20 app.log
```

常见字段：

| 位置 | 含义 |
|---|---|
| 第 1 列 | 文件类型和权限 |
| 第 2 列 | 硬链接数量 |
| 第 3 列 | 所有者 |
| 第 4 列 | 所属用户组 |
| 第 5 列 | 文件大小 |
| 第 6-8 列 | 修改时间 |
| 最后一列 | 文件或目录名称 |

本章暂时重点关注文件名、目录名、大小和时间；权限字段会在后续章节专门学习。

## 2-2.4 通配符

通配符用于匹配一组文件或目录名称：

| 通配符 | 含义 | 示例 |
|---|---|---|
| `*` | 0 个或多个任意字符 | `ls *.txt` |
| `?` | 1 个任意字符 | `ls file?.txt` |
| `[abc]` | 匹配 `a`、`b`、`c` 中任意一个字符 | `ls file[abc].txt` |
| `[a-d]` | 匹配 `a` 到 `d` 中任意一个字符 | `ls file[a-d].txt` |

示例：

```shell
ls *.txt
ls abc*
ls file?.txt
ls [ab]*.txt
ls file[1-3].txt
```

注意：

- `*` 匹配的是文件名字符，不代表“所有目录层级”；
- 通配符由 Shell 先展开，再把匹配结果交给 `ls`；
- 没有匹配到文件时，不同 Shell 或配置可能显示错误信息；
- 文件名区分大小写，`test.txt` 和 `Test.txt` 不是同一个文件。

---

# 2-3 目录切换：`cd`

## 2-3.1 基本格式

```shell
cd [目录路径]
```

示例：

```shell
cd /etc
cd /var/log
cd ~/linux-practice
```

## 2-3.2 绝对路径与相对路径

### 绝对路径

从根目录 `/` 开始书写：

```shell
cd /home/itheima
cd /var/log
```

### 相对路径

以当前目录为起点书写：

```shell
cd dir1
cd ./dir1
cd dir1/dir2
```

常用特殊路径：

- `.`：当前目录；
- `..`：上一级目录；
- `~`：当前用户的家目录。

示例：

```shell
cd .
cd ..
cd ../logs
cd ~
cd
```

## 2-3.3 `cd -`

```shell
cd -
```

返回上一次所在的目录，适合在两个目录之间来回切换：

```shell
cd /etc
cd /var/log
cd -
```

## 2-3.4 目录切换的验证

每次切换后可以用 `pwd` 确认当前路径：

```shell
cd /var/log
pwd
```

排查路径错误时，建议先执行 `pwd`，再使用 `ls` 查看当前目录内容，不要只根据提示符猜测位置。

---

# 2-4 创建文件和目录

## 2-4.1 创建文件：`touch`

```shell
touch 文件名
```

示例：

```shell
touch test.txt
touch a.txt b.txt c.log
```

行为说明：

- 文件不存在：创建空文件；
- 文件已经存在：通常更新文件的修改时间，不会清空原有内容；
- 可以一次创建多个文件，文件名之间使用空格分隔。

检查结果：

```shell
ls -l test.txt
```

## 2-4.2 创建目录：`mkdir`

```shell
mkdir 目录名
```

示例：

```shell
mkdir dir1
mkdir dir2 dir3
```

## 2-4.3 创建多级目录：`mkdir -p`

```shell
mkdir -p dir1/dir2/dir3
```

`-p` 会在上级目录不存在时一并创建，适合初始化测试目录结构：

```shell
mkdir -p ~/linux-practice/logs/app
```

如果目录已经存在，直接使用 `mkdir` 通常会报错；使用 `mkdir -p` 可以避免因为上级目录已存在而失败。

---

# 2-5 删除、复制和移动

## 2-5.1 删除文件或目录：`rm`

```shell
rm [选项] 文件或目录
```

常用选项：

| 选项 | 含义 |
|---|---|
| `-i` | 删除前询问 |
| `-f` | 强制删除，忽略不存在的目标 |
| `-r` | 递归删除目录及其中内容 |

示例：

```shell
rm test.txt
rm -i test.txt
rm -r dir1
rm -rf dir1
```

注意：

1. `rm` 删除文件后通常不能从命令行直接恢复；
2. 删除目录需要使用 `-r`；
3. `rm -rf` 权限高、风险大，执行前应先使用 `pwd` 和 `ls` 确认当前路径；
4. 生产环境中不应对不明确的路径直接执行 `rm -rf`。

更稳妥的操作方式：

```shell
pwd
ls -lah
rm -ri ./target-dir
```

## 2-5.2 复制：`cp`

```shell
cp [选项] 源文件 目标文件
```

常用选项：

| 选项 | 含义 |
|---|---|
| `-f` | 强制覆盖目标文件 |
| `-i` | 覆盖前询问 |
| `-r` | 递归复制目录 |
| `-v` | 显示复制过程 |

复制文件：

```shell
cp a.txt b.txt
cp a.txt /tmp/
cp a.txt /tmp/new-name.txt
```

复制多个文件：

```shell
cp a.txt b.txt /tmp/
cp *.txt /tmp/
```

复制目录：

```shell
cp -r dir1 dir2
cp -r dir1 /tmp/
```

源路径和目标路径的含义需要区分：

- 目标是已存在目录：文件会复制到该目录下；
- 目标不存在：可能会被当作新的文件名或目录名；
- 复制目录时必须使用 `-r`。

## 2-5.3 移动和重命名：`mv`

```shell
mv [选项] 源文件 目标文件
```

移动文件：

```shell
mv a.txt /tmp/
mv a.txt /tmp/new-name.txt
```

重命名文件：

```shell
mv old.txt new.txt
```

移动目录：

```shell
mv dir1 /tmp/
```

常用选项：

- `-f`：强制覆盖；
- `-i`：覆盖前询问；
- `-v`：显示移动过程。

`mv` 与 `cp` 的区别：

| 命令 | 源文件是否保留 | 常见用途 |
|---|---|---|
| `cp` | 保留 | 复制备份、生成副本 |
| `mv` | 通常不保留原位置的文件 | 移动、重命名 |

---

# 2-6 查看文件内容

## 2-6.1 `cat`

`cat` 适合查看内容较少的文件：

```shell
cat test.txt
```

常用选项：

| 选项 | 含义 |
|---|---|
| `-n` | 给所有行显示行号 |
| `-b` | 只给非空行显示行号 |

示例：

```shell
cat -n test.txt
cat -b test.txt
```

`cat` 也可以合并文件：

```shell
cat a.txt b.txt > all.txt
```

但不要使用 `cat source.txt > source.txt`，这会先清空目标文件，再读取内容，最终可能导致源文件内容丢失。

## 2-6.2 `more`

`more` 适合分屏查看内容较多的文件：

```shell
more /var/log/messages
more app.log
```

常用按键：

| 按键 | 作用 |
|---|---|
| 空格 | 下一页 |
| Enter | 下一行 |
| `b` | 上一页 |
| `f` | 下一页 |
| `q` | 退出 |

测试工作中查看日志时，可以先用 `more` 浏览整体结构，再使用 `grep` 精确搜索错误关键字。

## 2-6.3 `grep`

`grep` 用于搜索文件中包含指定内容的行：

```shell
grep '关键字' 文件名
```

示例：

```shell
grep 'error' app.log
grep -n 'error' app.log
grep -i 'error' app.log
grep -v 'debug' app.log
```

常用选项：

| 选项 | 含义 |
|---|---|
| `-n` | 显示匹配行的行号 |
| `-i` | 忽略大小写 |
| `-v` | 显示不匹配的行 |

## 2-6.4 基础匹配规则

```shell
grep '^a' test.txt
grep 'ke$' test.txt
grep '[Ss]ignal' test.txt
grep -n '[vV]Mware' messages.log
```

- `^a`：匹配以 `a` 开头的行；
- `ke$`：匹配以 `ke` 结尾的行；
- `[Ss]ignal`：匹配 `Signal` 或 `signal`；
- `[vV]Mware`：匹配 `VMware` 或 `vmware`。

注意：`grep` 的匹配模式不是普通的“包含字符串”这么简单。`^`、`$` 和 `[]` 会改变匹配规则，排查日志时要确认关键字的位置和大小写要求。

---

# 2-7 输出重定向

## 2-7.1 `>`

将命令输出覆盖写入文件：

```shell
ls -la /etc > etc-info.txt
```

如果 `etc-info.txt` 已存在，原有内容会被覆盖。

## 2-7.2 `>>`

将命令输出追加到文件末尾：

```shell
date >> operation.log
ls -la >> operation.log
```

如果目标文件不存在，通常会创建文件。

## 2-7.3 两者区别

| 符号 | 行为 | 适用场景 |
|---|---|---|
| `>` | 覆盖 | 生成新的结果文件 |
| `>>` | 追加 | 持续记录日志或多个命令结果 |

示例：

```shell
cat 1.txt > 2.txt
cat 1.txt >> 2.txt
cat 1.txt 2.txt > 3.txt
```

重定向的风险：

- 目标文件写错会覆盖重要内容；
- 目标文件和输入文件相同，可能导致数据丢失；
- 权限不足时会出现无法写入；
- 路径不存在时，重定向无法创建缺失的父目录。

安全检查：

```shell
pwd
ls -l target.txt
```

确认无误后再执行覆盖写入。

---

# 2-8 管道符：`|`

## 2-8.1 基本概念

管道符把前一个命令的标准输出传递给下一个命令处理：

```shell
前一个命令 | 后一个命令
```

它不是把两个命令简单地连续执行，而是让后一个命令直接接收前一个命令产生的结果。

## 2-8.2 分页显示

```shell
ls -l /usr/bin | more
```

先通过 `ls -l` 生成目录列表，再交给 `more` 分页查看。

## 2-8.3 过滤显示

```shell
ls /usr/bin | grep '^au'
ls /usr/bin | grep 'zip$'
```

含义：

- `^au`：筛选以 `au` 开头的名称；
- `zip$`：筛选以 `zip` 结尾的名称。

## 2-8.4 测试排查中的组合

```shell
ls -l /var/log | grep '.log'
ps -ef | grep nginx
netstat -anpt | grep 3306
```

第二、三条命令会在后续章节继续学习；本章重点是掌握“命令输出通过管道交给另一个命令筛选”的思路。

---

# 2-9 命令组合练习

## 2-9.1 初始化练习目录

```shell
mkdir -p ~/linux-practice/chapter2
cd ~/linux-practice/chapter2
touch app.log app.txt api.txt readme.md
mkdir source backup
```

## 2-9.2 查看和筛选

```shell
ls -lah
ls *.txt
ls | grep 'app'
```

## 2-9.3 复制和移动

```shell
cp app.txt backup/
mv api.txt source/api.txt
mv readme.md README.md
```

## 2-9.4 重定向和查看

```shell
ls -lah > files.txt
cat files.txt
grep -n 'app' files.txt
```

## 2-9.5 清理练习目录

```shell
cd ~
rm -ri ~/linux-practice/chapter2
```

练习结束后，先用 `ls` 确认删除目标，再执行删除命令。

---

# 2-10 课后练习与参考答案

## 2-10.1 理论题

### 题 1：`ls -l` 和 `ls -a` 有什么区别？

**参考答案**：`-l` 以详细列表形式显示文件信息；`-a` 显示所有文件，包括隐藏文件。二者可以组合为 `ls -la`。

### 题 2：绝对路径和相对路径有什么区别？

**参考答案**：绝对路径从 `/` 或 `~` 开始，位置固定；相对路径以当前目录为起点，不以 `/` 或 `~` 开头，当前目录变化后同一相对路径可能指向不同位置。

### 题 3：`>` 和 `>>` 的区别是什么？

**参考答案**：`>` 覆盖写入目标文件，`>>` 追加写入目标文件。目标文件不存在时，两者通常都会创建文件，但父目录必须已经存在。

### 题 4：`cp` 和 `mv` 的区别是什么？

**参考答案**：`cp` 复制文件或目录并保留源文件；`mv` 移动文件或目录，也可以在同一目录中用于重命名。

### 题 5：查看大文件时，`cat` 和 `more` 应如何选择？

**参考答案**：内容较少时使用 `cat` 一次性显示；内容较多时使用 `more` 分页查看，避免大量内容瞬间刷过屏幕。

## 2-10.2 实操题

### 题 1：将 `/usr/bin` 目录下所有内容的详细信息重定向到 `info`

```shell
ls -l /usr/bin > info
```

如果希望保存到指定目录：

```shell
ls -l /usr/bin > /tmp/info
```

### 题 2：将 `/usr/bin` 目录下所有内容的详细信息分屏显示

```shell
ls -l /usr/bin | more
```

### 题 3：Linux 下获取 IP 地址

课程环境中可以使用：

```shell
ifconfig
```

较新的 Linux 系统也常用：

```shell
ip addr
```

### 题 4：查看所有用户的进程

本题会涉及下一章的进程命令，常见写法是：

```shell
ps -aux
```

或：

```shell
ps -ef
```

### 题 5：在 `/home/itheima` 创建 `mike` 及 4 个文件，并压缩为 `test.tar.gz`

```shell
mkdir -p /home/itheima/mike
touch /home/itheima/mike/1.txt \
      /home/itheima/mike/2.txt \
      /home/itheima/mike/3.txt \
      /home/itheima/mike/4.txt
tar -zcvf /home/itheima/test.tar.gz -C /home/itheima mike
```

这里使用 `-C /home/itheima`，可以让压缩包中的路径从 `mike` 开始，避免把绝对路径信息写入压缩包。

### 题 6：将 `test.tar.gz` 解压到 `/opt`

```shell
tar -zxvf /home/itheima/test.tar.gz -C /opt
```

执行前确认当前用户具有写入 `/opt` 的权限。

### 题 7：修改 `mike` 下所有 `.txt` 文件权限

题目要求：

- 所有者：可读、可执行，即 `r-x`，数字为 `5`；
- 用户组：可写、可执行，即 `-wx`，数字为 `3`；
- 其他用户：可执行，即 `--x`，数字为 `1`。

命令：

```shell
chmod 531 /home/itheima/mike/*.txt
```

说明：权限数字由 `r=4`、`w=2`、`x=1` 相加得到：

```text
r-x = 4 + 1 = 5
-wx = 2 + 1 = 3
--x = 1
```

### 题 8：查看 3306 端口是否被占用

```shell
netstat -anptu | grep '3306'
```

或者：

```shell
lsof -i:3306
```

如果没有输出，通常表示当前命令范围内没有查到占用该端口的进程；仍需结合权限、系统工具和服务状态判断。

---

# 2-11 常见错误与排查

## 错误 1：目录不存在

现象：

```text
No such file or directory
```

排查：

```shell
pwd
ls -la
```

确认路径拼写、大小写和当前目录是否正确。

## 错误 2：权限不足

现象：

```text
Permission denied
```

排查重点：

- 当前登录用户是谁；
- 目标文件或目录的权限；
- 是否需要切换到具有权限的用户；
- 是否可以通过更合理的目录或权限配置解决，而不是直接使用管理员权限。

## 错误 3：复制目录没有使用 `-r`

错误示例：

```shell
cp dir1 dir2
```

复制目录通常应写为：

```shell
cp -r dir1 dir2
```

## 错误 4：重定向覆盖了原文件

错误示例：

```shell
cat app.log > app.log
```

这是危险操作，可能导致文件被清空。需要将结果写入不同的目标文件：

```shell
cat app.log > app-copy.log
```

## 错误 5：`grep` 匹配结果不符合预期

检查以下因素：

- 是否需要忽略大小写，使用 `-i`；
- 是否需要显示行号，使用 `-n`；
- 是否误用了 `^` 或 `$`；
- 关键字是否包含特殊字符；
- 是否把文件路径写错。

---

# 2-12 高频命令速查表

| 目的 | 命令 |
|---|---|
| 查看目录 | `ls` |
| 查看详细信息 | `ls -l` |
| 查看隐藏文件 | `ls -a` |
| 易读方式显示大小 | `ls -h` |
| 查看所有详细信息 | `ls -lah` |
| 进入目录 | `cd dir1` |
| 返回上一级 | `cd ..` |
| 返回家目录 | `cd` 或 `cd ~` |
| 返回上一次目录 | `cd -` |
| 创建文件 | `touch test.txt` |
| 创建目录 | `mkdir dir1` |
| 创建多级目录 | `mkdir -p a/b/c` |
| 删除文件 | `rm test.txt` |
| 递归删除目录 | `rm -r dir1` |
| 复制文件 | `cp a.txt b.txt` |
| 复制目录 | `cp -r dir1 dir2` |
| 移动或重命名 | `mv old.txt new.txt` |
| 查看小文件 | `cat test.txt` |
| 分页查看 | `more app.log` |
| 搜索关键字 | `grep -n 'error' app.log` |
| 覆盖写入 | `command > result.txt` |
| 追加写入 | `command >> result.txt` |
| 分页处理 | `command | more` |
| 过滤处理 | `command | grep 'keyword'` |

---

# 2-13 本章验收标准

完成本章后，至少应能在 Linux 环境中独立完成以下任务：

1. 创建一个练习目录，并在其中建立多级目录；
2. 创建多个不同扩展名的文件；
3. 使用 `ls -lah` 查看隐藏文件和详细信息；
4. 使用 `*`、`?`、`[]` 筛选文件；
5. 使用绝对路径和相对路径切换目录；
6. 复制文件和目录，并验证复制结果；
7. 移动文件、目录并完成重命名；
8. 使用 `cat`、`more` 查看文件；
9. 使用 `grep` 按关键字、行号和大小写筛选内容；
10. 通过 `>` 和 `>>` 保存命令执行结果；
11. 通过管道符实现分页和过滤；
12. 对删除和覆盖写入操作进行路径确认，避免误删和误覆盖。

---

# 2-14 测试工作中的应用

## 场景 1：收集环境目录信息

```shell
pwd
ls -lah /opt/app
ls -lah /opt/app/config
```

用于确认应用部署目录、配置文件和日志文件是否存在。

## 场景 2：筛选日志文件

```shell
ls -lah /var/log | grep '.log'
grep -n -i 'error' /var/log/app.log
```

用于快速确认日志位置并搜索错误信息。

## 场景 3：保存排查证据

```shell
ls -lah /opt/app > /tmp/app-files.txt
grep -n -i 'timeout' /var/log/app.log >> /tmp/app-errors.txt
```

排查时建议记录执行时间、服务器、命令和结果，便于提交缺陷和复现问题。

## 场景 4：准备测试文件

```shell
mkdir -p /tmp/test-data
touch /tmp/test-data/valid.txt
touch /tmp/test-data/empty.txt
cp /tmp/test-data/valid.txt /tmp/test-data/valid-backup.txt
```

测试文件建议放在明确的临时目录中，避免误修改应用配置或生产数据。
