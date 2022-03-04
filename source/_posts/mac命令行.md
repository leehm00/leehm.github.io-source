---
title: mac命令行
top: false
cover: false
toc: true
mathjax: true
tags:
  - 命令端
categories:
  - 日常
typora-root-url: ../
date: 2022-03-02 11:05:04
password:
summary:
---

### man

一条命令`man`解决所有问题。

`man command-name`进入命令指南页面，`上箭头或下箭头`上下移动，使用`空格`来翻页，输入 `/` 和关键字来按照关键字搜索，按 `Q` 来退出使用指南页面。

输入 `man -k` 和关键字来对整个使用指南数据库进行搜索。

以下都是可以用`man`查看具体用法的：

### 目录操作

| 命令名 | 功能描述             | 使用举例         |
| ------ | -------------------- | ---------------- |
| mkdir  | 创建一个目录         | mkdir dirname    |
| rmdir  | 删除一个目录         | rmdir dirname    |
| mvdir  | 移动或重命名一个目录 | mvdir dir1 dir2  |
| cd     | 改变当前目录         | cd dirname       |
| pwd    | 显示当前目录的路径名 | pwd              |
| ls     | 显示当前目录的内容   | ls -la           |
| dircmp | 比较两个目录的内容   | dircmp dir1 dir2 |

#### 文件操作

| 命令名 | 功能描述                                                     | 使用举例                  |
| ------ | ------------------------------------------------------------ | ------------------------- |
| cat    | 显示或连接文件，可以用`>>`来增加文本文件内容                 | cat filename              |
| pg     | 分页格式化显示文件内容                                       | pg filename               |
| more   | 分屏显示文件内容                                             | more filename             |
| od     | 显示非文本文件的内容                                         | od -c filename            |
| cp     | 复制文件或目录                                               | cp file1 file2            |
| rm     | 删除文件或目录                                               | rm filename               |
| mv     | 改变文件名或所在目录                                         | mv file1 file2            |
| ln     | 联接文件                                                     | ln -s file1 file2         |
| find   | 使用匹配表达式查找文件                                       | find . -name "*.c" -print |
| file   | 显示文件类型                                                 | file filename             |
| open   | 使用默认的程序打开文件                                       | open filename             |
| Less   | 查看长文本文件，因为它可以查找文本。语法为 `less` 后接文件路径，和 `cat` 一样。用 `less` 命令打开的文件其实和你查看命令使用指南的时候使用的是一个查看器 | less filename             |
| which  | 定位某个命令的文件路径。它会告诉你你执行某个具体命令的时候，在使用哪个文件。语法为 `which` 后接某个命令。 | Which man                 |

#### **使用通配符（Wildcard Characters）**

- 星号（＊，Asterisk）——代表任何长度的任何字符。例如 `*.tiff` 代表所有格式为tiff的文件。
- 问号（?，Question mark）——代表任何单个字符。例如 `b?ok` 匹配 book 但是不匹配 brook。
- 方括号（[]，Square brackets）——定义一定范围的字符，例如 `[Dd]ocument` 匹配 Document 以及 document；`doc[1-9]` 匹配doc1, doc2, …, doc9。

#### **使用递归命令**

简单来说，递归命令可以允许命令不执行于一个特定文件，而是指定的路径下的所有文件。大多数命令包含一个 `-r` 或者 `-R` 选项，来设定你想递归地执行这个命令。例如下面的例子，添加 `-R` 后 `ls` 命令的执行方式：`ls -R Public`，这会列出 Public 中的所有文件，若有文件夹，则会继续列出文件夹中的内容。

### 选择操作

| 命令名 | 功能描述                       | 使用举例                     |
| ------ | ------------------------------ | ---------------------------- |
| head   | 显示文件的最初几行             | head -20 filename            |
| tail   | 显示文件的最后几行             | tail -15 filename            |
| cut    | 显示文件每行中的某些域         | cut -f1,7 -d: /etc/passwd    |
| colrm  | 从标准输入中删除若干列         | colrm 8 20 file2             |
| paste  | 横向连接文件                   | paste file1 file2            |
| diff   | 比较并显示两个文件的差异       | diff file1 file2             |
| sed    | 非交互方式流编辑器             | sed "s/red/green/g" filename |
| grep   | 在文件中按模式查找             | grep "^[a-zA-Z]" filename    |
| awk    | 在文件中查找并处理模式         | awk '{print $1 $1}' filename |
| sort   | 排序或归并文件                 | sort -d -f -u file1          |
| uniq   | 去掉文件中的重复行             | uniq file1 file2             |
| comm   | 显示两有序文件的公共和非公共行 | comm file1 file2             |
| wc     | 统计文件的字符数、词数和行数   | wc filename                  |
| nl     | 给文件加上行号                 | nl file1 >file2              |

### 安全操作

| 命令名 | 功能描述               | 使用举例                |
| ------ | ---------------------- | ----------------------- |
| passwd | 修改用户密码           | passwd                  |
| chmod  | 改变文件或目录的权限   | chmod ug+x filename     |
| umask  | 定义创建文件的权限掩码 | umask 027               |
| chown  | 改变文件或目录的属主   | chown newowner filename |
| chgrp  | 改变文件或目录的所属组 | chgrp staff filename    |
| xlock  | 给终端上锁             | xlock -remote           |

### 编程操作

| 命令名 | 功能描述                 | 使用举例                   |
| ------ | ------------------------ | -------------------------- |
| make   | 维护可执行程序的最新版本 | make                       |
| touch  | 更新文件的访问和修改时间 | touch -m 05202400 filename |
| dbx    | 命令行界面调试工具       | dbx a.out                  |
| xde    | 图形用户界面调试工具     | xde a.out                  |

### 进程操作

| 命令名 | 功能描述               | 使用举例         |
| ------ | ---------------------- | ---------------- |
| ps     | 显示进程当前状态       | ps u             |
| kill   | 终止进程               | kill -9 30142    |
| nice   | 改变待执行命令的优先级 | nice cc -c *.c   |
| renice | 改变已运行进程的优先级 | renice +20 32768 |

### 时间操作

| 命令名 | 功能描述                 | 使用举例   |
| ------ | ------------------------ | ---------- |
| date   | 显示系统的当前日期和时间 | date       |
| cal    | 显示日历                 | cal 8 1996 |
| time   | 统计程序的执行时间       | time a.out |

### 网络与通信操作

| 命令名 | 功能描述                          | 使用举例                    |
| ------ | --------------------------------- | --------------------------- |
| telnet | 远程登录                          | telnet hpc.sp.net.edu.cn    |
| rlogin | 远程登录                          | rlogin hostname -l username |
| rsh    | 在远程主机执行指定命令            | rsh f01n03 date             |
| ftp    | 在本地主机与远程主机之间传输文件  | ftp ftp.sp.net.edu.cn       |
| rcp    | 在本地主机与远程主机 之间复制文件 | rcp file1 host1:file2       |
| ping   | 给一个网络主机发送 回应请求       | ping hpc.sp.net.edu.cn      |
| mail   | 阅读和发送电子邮件                | mail                        |
| write  | 给另一用户发送报文                | write username pts/1        |
| mesg   | 允许或拒绝接收报文                | mesg n                      |

### Korn Shell 命令

| 命令名  | 功能描述                        | 使用举例        |
| ------- | ------------------------------- | --------------- |
| history | 列出最近执行过的 几条命令及编号 | history         |
| r       | 重复执行最近执行过的 某条命令   | r -2            |
| alias   | 给某个命令定义别名              | alias del=rm -i |
| unalias | 取消对某个别名的定义            | unalias del     |

### 其它命令

| 命令名 | 功能描述                       | 使用举例     |
| ------ | ------------------------------ | ------------ |
| uname  | 显示操作系统的有关信息         | uname -a     |
| clear  | 清除屏幕或窗口内容             | clear        |
| env    | 显示当前所有设置过的环境变量   | env          |
| who    | 列出当前登录的所有用户         | who          |
| whoami | 显示当前正进行操作的用户名     | whoami       |
| tty    | 显示终端或伪终端的名称         | tty          |
| stty   | 显示或重置控制键定义           | stty -a      |
| du     | 查询磁盘使用情况               | du -k subdir |
| df     | 显示文件系统的总空间和可用空间 | df /tmp      |
| w      | 显示当前系统活动的总信息       | w            |



## 其他 Command-Line 技巧提示

- 输入命令 `open .` 可以打开当前的位置。
- 在 Terminal 的偏好里面可以设定它的外观和风格。
- 中止一个正在执行的命令，可以使用组合键 control + C。
- 在执行前编辑命令，只需要使用箭头和键盘上的其他字母。
- 没有输入任何命令时，你可以用▲和▼来浏览历史命令。同样可以编辑和再次执行。
- 你也可以使用history命令查看历史记录。
- 你可以使用组合键 control + L 清屏。

参考链接：https://www.jianshu.com/p/a9bf79b1b134

### 
