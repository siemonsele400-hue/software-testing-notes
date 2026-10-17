# 阶段二：Linux 和数据库

## 第一章：Linux 操作系统基础

本章是阶段二的入口，目标是建立 Linux 基础操作能力。对测试人员来说，Linux 不只是“会敲几个命令”，而是后续进行服务部署、进程与端口排查、日志分析、数据库连接和项目验证的基础环境。

本章对应课程视频目录“第一章 Linux 操作系统基础”，以及配套资料中的 `Linux数据库day01`、Linux 第一章讲义和 day01 作业。

---

# 1-1 为什么学习 Linux

## 1-1.1 Linux 在测试工作中的位置

很多实际项目的应用服务器、数据库服务器、日志服务器和持续集成环境都运行在 Linux 上。测试人员需要在 Linux 环境中完成以下工作：

- 连接测试服务器，确认环境是否可用；
- 查看应用部署目录和配置文件；
- 启动、停止或检查服务；
- 查看进程、端口和日志；
- 准备测试文件和测试数据；
- 配合数据库、接口和项目部署进行验证。

因此，Linux 基础命令是测试人员定位问题的重要工具。页面出现错误时，不能只停留在“页面报错”这一现象，还需要继续查看接口响应、服务日志、进程状态和数据库数据。

## 1-1.2 Linux 的主要特点

| 特点 | 含义 |
|---|---|
| 开源免费 | 源代码开放，发行版和工具生态丰富 |
| 安全稳定 | 权限体系清晰，适合长期运行服务器 |
| 可移植性好 | 可以运行在服务器、虚拟机、嵌入式设备等多种环境 |
| 高性能 | 资源占用相对可控，适合承载服务和数据库 |

## 1-1.3 常见应用领域

- 应用服务器
- 数据库服务器
- 网络服务器
- 虚拟化和云计算
- 嵌入式设备
- 个人电脑
- 移动设备

---

# 1-2 Linux 文件系统与目录

## 1-2.1 树状目录结构

Linux 的目录结构从唯一的根目录 `/` 开始，逐层向下组织文件和目录：

```text
/
├── bin
├── boot
├── etc
├── home
├── root
├── tmp
├── usr
└── var
```

## 1-2.2 常见目录

| 目录 | 作用 |
|---|---|
| `/` | 根目录，整个文件系统的起点 |
| `/home` | 普通用户的家目录，例如 `/home/itheima` |
| `/root` | `root` 管理员用户的家目录 |
| `/etc` | 系统和服务配置文件 |
| `/usr` | 系统应用程序、命令和共享资源 |
| `/boot` | 启动相关文件和内核文件 |
| `/tmp` | 临时文件目录 |
| `/var` | 经常变化的数据，例如日志和缓存 |

## 1-2.3 路径概念

### 绝对路径

从根目录 `/` 或家目录 `~` 开始书写的完整路径：

```shell
/home/itheima
/etc/hosts
~/test.txt
```

### 相对路径

以当前目录为起点书写的路径，不以 `/` 或 `~` 开头：

```shell
./test.txt
../other
dir1/dir2
```

常见特殊路径：

- `.`：当前目录
- `..`：上一级目录
- `~`：当前用户的家目录

---

# 1-3 Linux 远程连接

## 1-3.1 为什么需要远程连接

工作中的 Linux 服务器通常位于机房或云端，测试人员不一定能直接操作服务器所在的物理电脑，因此需要使用远程工具连接服务器。

常见连接条件：

1. 已知服务器 IP 地址；
2. 网络可以访问服务器；
3. 服务器 SSH 服务已启动；
4. 拥有可用的用户名、密码或密钥；
5. 知道 SSH 端口，默认通常是 `22`。

## 1-3.2 获取和检查 IP

在 Linux 中可以使用以下命令查看网络信息：

```shell
ifconfig
```

部分新系统也可以使用：

```shell
ip addr
```

从本地电脑测试网络连通性：

```shell
ping 服务器IP
```

检查 Linux 中 SSH 服务是否监听：

```shell
netstat -anpt | grep ssh
```

> 实际工作中，服务器 IP、端口和账号通常由运维或环境管理员提供。不要把课程中的示例账号密码直接用于真实环境。

## 1-3.3 远程连接信息

使用 Xshell、FinalShell、SecureCRT 或其他 SSH 客户端时，一般需要配置：

| 配置项 | 说明 |
|---|---|
| Host/IP | Linux 服务器地址 |
| Port | SSH 端口，默认 `22` |
| User name | 登录用户，例如普通用户或 `root` |
| Password/Key | 密码或 SSH 密钥 |

测试排查时，建议优先使用权限足够但范围受控的普通账号，只有确实需要时才使用管理员权限。

---

# 1-4 Linux 命令基础

## 1-4.1 命令格式

```shell
command [-options] [parameter]
```

- `command`：命令名称；
- `-options`：选项，用于改变命令行为，可省略；
- `parameter`：参数，通常是文件、目录或关键字，可省略；
- 方括号表示可选内容，不是要求实际输入的符号。

示例：

```shell
ls -lah /var/log
```

## 1-4.2 命令行操作习惯

- Linux 命令和路径通常区分大小写；
- 按 `Tab` 自动补全命令、路径和文件名；
- 按上下方向键查看历史命令；
- `Ctrl + C`：中断当前操作；
- `Ctrl + L`：清屏；
- 可以先输入命令，再根据错误信息判断路径、权限或参数是否正确。

## 1-4.3 查看帮助

### `--help`

查看相对简洁的帮助信息：

```shell
ls --help
```

### `man`

查看更完整的命令手册：

```shell
man ls
```

`man` 中的常用操作：

| 按键 | 作用 |
|---|---|
| 空格 | 下一页 |
| Enter | 下一行 |
| `b` | 上一页 |
| `f` | 下一页 |
| `/word` | 搜索关键字 |
| `q` | 退出手册 |

---

# 1-5 文件和目录常用命令

## 1-5.1 查看目录：`ls`

```shell
ls [-lah] [文件或目录]
```

常用选项：

- `-l`：以详细列表形式显示；
- `-a`：显示所有内容，包括隐藏文件；
- `-h`：以更易读的方式显示文件大小。

常用示例：

```shell
ls
ls -l
ls -la
ls -lah /var/log
```

### 通配符

| 通配符 | 含义 |
|---|---|
| `*` | 匹配 0 个或多个任意字符 |
| `?` | 匹配 1 个任意字符 |
| `[abcd]` | 匹配括号内任意一个字符 |
| `[a-d]` | 匹配 `a` 到 `d` 范围内的一个字符 |

示例：

```shell
ls *.txt
ls file?.txt
ls [ab]*.txt
ls [a-d].txt
```

## 1-5.2 切换目录：`cd`

```shell
cd [目录路径]
```

示例：

```shell
cd /etc
cd ..
cd ~
cd
cd -
cd ./dir1
```

- `cd` 或 `cd ~`：进入当前用户家目录；
- `cd ..`：返回上一级目录；
- `cd -`：返回上一次所在目录。

## 1-5.3 创建文件：`touch`

```shell
touch 文件名
```

文件不存在时创建空文件；文件已存在时，通常会更新文件的修改时间。

```shell
touch test.txt a.txt b.txt
```

## 1-5.4 创建目录：`mkdir`

```shell
mkdir 目录名
```

一次创建多级目录时使用 `-p`：

```shell
mkdir -p dir1/dir2/dir3
```

如果目标目录已经存在，直接创建通常会失败；可以根据需要检查目录是否存在，避免误操作。

## 1-5.5 删除：`rm`

```shell
rm [-irf] 文件或目录
```

| 选项 | 作用 |
|---|---|
| `-i` | 删除前询问确认 |
| `-f` | 强制删除，忽略不存在的文件 |
| `-r` | 递归删除目录及其内容 |

示例：

```shell
rm test.txt
rm -i test.txt
rm -r dir1
rm -rf dir1
```

> `rm -rf` 删除不可恢复。实际操作前应确认当前目录和目标路径，生产环境中尤其要避免直接对不明确的路径使用该命令。

## 1-5.6 复制：`cp`

```shell
cp [选项] 源文件 目标文件
```

常用选项：

- `-f`：强制覆盖；
- `-i`：覆盖前询问；
- `-r`：递归复制目录；
- `-v`：显示复制过程。

示例：

```shell
cp a.txt b.txt
cp a.txt /tmp/
cp -r dir1 dir2
```

## 1-5.7 移动和重命名：`mv`

```shell
mv [选项] 源文件 目标文件
```

`mv` 既可以移动文件或目录，也可以修改名称：

```shell
mv a.txt /tmp/
mv old.txt new.txt
mv dir1 /tmp/
```

常用选项：

- `-f`：强制覆盖；
- `-i`：覆盖前询问；
- `-v`：显示移动过程。

---

# 1-6 查看文件内容

## 1-6.1 `cat`

适合查看内容较少的文件，也可以用于合并或重定向文件内容：

```shell
cat test.txt
cat -n test.txt
cat -b test.txt
```

- `-n`：显示所有行号；
- `-b`：只给非空行显示行号。

## 1-6.2 `more`

适合分屏查看较大的文件：

```shell
more /var/log/mysqld.log
```

常用按键：

- 空格：下一页；
- Enter：下一行；
- `b`：上一页；
- `f`：下一页；
- `q`：退出。

## 1-6.3 `grep`

用于搜索文件中包含指定关键字的行：

```shell
grep '关键字' 文件名
grep -n 'error' app.log
```

常用选项：

- `-v`：显示不匹配的行；
- `-n`：显示行号；
- `-i`：忽略大小写。

常见匹配写法：

```shell
grep '^a' test.txt
grep 'ke$' test.txt
grep -n '[Ss]igna[Ll]' test.txt
grep -n '[vV]Mware' /var/log/messages
```

- `^a`：以 `a` 开头；
- `ke$`：以 `ke` 结尾；
- `[Ss]`：匹配 `S` 或 `s`；
- `[vV]`：匹配 `v` 或 `V`。

---

# 1-7 重定向与管道

## 1-7.1 输出重定向

| 符号 | 作用 |
|---|---|
| `>` | 覆盖写入目标文件 |
| `>>` | 追加写入目标文件 |

示例：

```shell
cat 1.txt > 2.txt
cat 1.txt >> 2.txt
cat 1.txt 2.txt > 3.txt
```

注意：`>` 会清空目标文件原有内容后再写入；日志保存或结果收集时，如果不希望覆盖，应使用 `>>`。

## 1-7.2 管道符 `|`

管道符把前一个命令的输出传给后一个命令继续处理：

```shell
ls -l /usr/bin | more
ls /usr/bin | grep '^au'
```

常见组合：

```shell
ps -ef | grep nginx
ls -l /var/log | grep '.log'
netstat -anpt | grep ssh
```

管道的核心思路是“先产生结果，再筛选、分页或继续处理”，是 Linux 排查问题时最常用的组合方式之一。

## 1-7.3 其他常用命令

```shell
pwd
clear
which ls
```

- `pwd`：显示当前所在目录；
- `clear`：清空终端显示；
- `which`：查找命令或工具的安装位置。

---

# 1-8 测试人员的 Linux 排查思路

遇到“服务访问失败”或“页面出现异常”时，可以按以下顺序快速检查：

1. **确认当前环境**：使用 `pwd`、`hostname` 等命令确认自己连接的是哪台机器；
2. **确认网络连通**：检查 IP、端口和 SSH 连接；
3. **确认服务进程**：使用进程命令查看服务是否启动；
4. **确认监听端口**：检查服务是否监听预期端口；
5. **查看日志**：用 `cat`、`more`、`grep` 搜索异常关键字；
6. **确认配置和文件**：使用 `ls`、`cd`、`which` 定位配置、程序和日志；
7. **保留排查证据**：记录命令、时间、日志片段和现象，便于提交缺陷或与开发沟通。

本章暂时只覆盖基础命令；后续章节会继续学习进程、端口、日志、权限、压缩和 `vi` 编辑器。

---

# 1-9 高频命令速查表

| 目的 | 命令示例 |
|---|---|
| 查看当前路径 | `pwd` |
| 查看目录详情 | `ls -lah` |
| 进入目录 | `cd /etc` |
| 返回上一级 | `cd ..` |
| 返回家目录 | `cd` 或 `cd ~` |
| 创建文件 | `touch test.txt` |
| 创建多级目录 | `mkdir -p a/b/c` |
| 删除文件 | `rm test.txt` |
| 删除目录 | `rm -r dir1` |
| 复制文件 | `cp a.txt b.txt` |
| 复制目录 | `cp -r dir1 dir2` |
| 移动或改名 | `mv old.txt new.txt` |
| 查看小文件 | `cat test.txt` |
| 分页查看 | `more app.log` |
| 搜索关键字 | `grep -n 'error' app.log` |
| 覆盖写入 | `command > result.txt` |
| 追加写入 | `command >> result.txt` |
| 管道筛选 | `command | grep '关键字'` |
| 查看命令位置 | `which command` |

---

# 1-10 课后练习与参考答案

## 1-10.1 理论题

### 题 1：什么是操作系统？

**参考答案**：操作系统是管理计算机硬件和软件资源、为应用程序提供运行环境并为用户提供操作接口的系统软件。Linux 是操作系统的一类，常用于服务器、数据库和云计算环境。

### 题 2：Linux 主要应用领域有哪些？

**参考答案**：应用服务器、数据库服务器、网络服务器、虚拟化和云计算、嵌入式设备、个人电脑和移动设备等。

### 题 3：简述 Linux 文件系统结构。

**参考答案**：Linux 采用以 `/` 为根的树状目录结构。`/home` 存放普通用户家目录，`/root` 是管理员家目录，`/etc` 存放配置文件，`/usr` 存放应用程序和共享资源，`/boot` 存放启动相关文件，`/tmp` 存放临时文件。

### 题 4：常用的文件和目录命令有哪些？

**参考答案**：`ls`、`cd`、`touch`、`mkdir`、`rm`、`cp`、`mv`、`cat`、`more`、`grep`、`pwd`、`clear`、`which`，以及重定向符号 `>`、`>>` 和管道符 `|`。

## 1-10.2 实操题

### 题 1：进入管理员用户目录

```shell
cd /root
```

### 题 2：查看 `/home/itheima` 下所有文件的详细信息

绝对路径：

```shell
ls -la /home/itheima
```

如果当前位于 `/home`，也可以使用相对路径：

```shell
cd /home
ls -la ./itheima
```

### 题 3：在 `/root` 创建 `adir/dir1`

```shell
mkdir -p /root/adir/dir1
```

### 题 4：在 `dir1` 下创建 `test.txt`

```shell
touch /root/adir/dir1/test.txt
```

### 题 5：在 `dir1` 下创建 `abc.txt` 和 `abd.csv`

```shell
touch /root/adir/dir1/abc.txt /root/adir/dir1/abd.csv
```

### 题 6：查看 `dir1` 下所有 `.txt` 文件的详细信息并写入 `test.txt`

题目将结果写入同一目录下的 `test.txt`，而 `test.txt` 本身也符合 `*.txt`，可能造成结果包含自身。更稳妥的写法是先将结果写入临时文件，再移动或检查结果：

```shell
ls -l /root/adir/dir1/*.txt > /tmp/dir1_txt_list.txt
mv /tmp/dir1_txt_list.txt /root/adir/dir1/test.txt
```

如果只按课堂基础命令直接完成，可写为：

```shell
ls -l /root/adir/dir1/*.txt > /root/adir/dir1/test.txt
```

实际工作中应注意重定向目标会被覆盖，并确认结果文件是否需要排除自身。

### 题 7：创建 `dir2/1.txt` 和 `dir2/2.txt`

```shell
mkdir -p /root/adir/dir2
touch /root/adir/dir2/1.txt /root/adir/dir2/2.txt
```

### 题 8：将 `dir2` 下所有文件复制到 `dir1`

```shell
cp /root/adir/dir2/* /root/adir/dir1/
```

### 题 9：删除 `dir2` 及其内容

```shell
rm -rf /root/adir/dir2
```

执行前必须确认目标路径，避免误删其他目录。

### 题 10：通过管道查看 `/usr/bin` 中以 `zip` 结尾的文件

```shell
ls /usr/bin | grep 'zip$'
```

### 题 11：查看 `/etc` 文件信息并写入 `abc.txt`

```shell
ls -la /etc > abc.txt
```

如果希望写入指定位置：

```shell
ls -la /etc > /root/adir/abc.txt
```

---

# 1-11 本章验收标准

完成本章后，应能够独立完成以下任务：

1. 解释 Linux 的特点、常见应用场景和树状目录结构；
2. 区分绝对路径和相对路径，正确使用 `/`、`~`、`.` 和 `..`；
3. 说明 SSH 远程连接需要的基本信息；
4. 读懂 `command [-options] [parameter]` 命令格式；
5. 使用 `ls`、`cd`、`touch`、`mkdir`、`rm`、`cp`、`mv` 操作文件和目录；
6. 使用 `cat`、`more`、`grep` 查看和过滤文件内容；
7. 正确区分 `>` 覆盖写入和 `>>` 追加写入；
8. 使用 `|` 将多个命令组合起来；
9. 能够完成 day01 作业中的目录、文件、复制、删除、重定向和过滤任务；
10. 遇到 Linux 环境问题时，能够留下可复现的命令和排查证据。

---

# 1-12 面试与工作补充

## Q1：`>` 和 `>>` 有什么区别？

`>` 会覆盖目标文件原有内容，`>>` 会在目标文件末尾追加内容。写日志或累积排查结果时通常使用 `>>`，需要生成全新的结果文件时可以使用 `>`。

## Q2：`cp` 和 `mv` 有什么区别？

`cp` 保留源文件并复制出副本；`mv` 会移动源文件，或者在同一目录下用于重命名。

## Q3：`rm -r` 和 `rm -f` 分别做什么？

`-r` 表示递归处理目录及其内容，`-f` 表示强制删除并忽略不存在的目标。删除非空目录通常需要组合使用 `rm -rf`，但该命令风险很高，必须先核对路径。

## Q4：如何查看某个命令在哪里？

```shell
which ls
```

也可以使用：

```shell
command -v ls
```

## Q5：测试人员为什么要学习 Linux？

因为测试环境和生产环境大量运行在 Linux 上。掌握 Linux 后，测试人员可以独立完成环境确认、文件操作、服务排查、日志过滤和问题证据收集，提升定位效率和沟通质量。

---

# 1-13 学习建议

不要只背命令，建议在一个临时目录中反复执行一套完整练习：

```shell
mkdir -p ~/linux-practice/adir/dir1
touch ~/linux-practice/adir/dir1/{abc.txt,abd.csv,test.txt}
ls -lah ~/linux-practice/adir/dir1
ls -l ~/linux-practice/adir/dir1/*.txt
cp ~/linux-practice/adir/dir1/abc.txt ~/linux-practice/adir/dir1/copy.txt
mv ~/linux-practice/adir/dir1/copy.txt ~/linux-practice/adir/dir1/moved.txt
grep -n 'txt' ~/linux-practice/adir/dir1/moved.txt
rm -rf ~/linux-practice/adir
```

注意：`{abc.txt,abd.csv,test.txt}` 属于 Bash 的花括号展开写法，不是本章必须掌握的通配符。若当前环境不支持，可分开执行多个 `touch` 命令。
