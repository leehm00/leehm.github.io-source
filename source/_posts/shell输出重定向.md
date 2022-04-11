---
title: shell输出重定向
top: false
cover: false
toc: true
mathjax: true
tags:
  - 学习
categories:
  - shell
typora-root-url: ../
date: 2022-04-04 23:39:34
password:
summary:
---

### shell 1>&2 2>&1 &>filename重定向的含义和区别:

1. 当初在shell中, 看到”>&1″和”>&2″始终不明白什么意思.经过在网上的搜索得以解惑.其实这是两种输出.
2. 在 shell 程式中，最常使用的 FD (file descriptor) 大概有三个, 分别是:

**0 是一个文件描述符，表示标准输入(stdin)**

**1 是一个文件描述符，表示标准输出(stdout)**

**2 是一个文件描述符，表示标准错误(stderr)**

1. 在标准情况下, 这些FD分别跟如下设备关联:

stdin(0): keyboard 键盘输入,并返回在前端

stdout(1): monitor 正确返回值 输出到前端

stderr(2): monitor 错误返回值 输出到前端

#### 举例说明吧:

当前目录只有一个文件 a.txt.

```javascript
[[email protected] box]# ls 
a.txt 
[[email protected] box]# ls a.txt b.txt 
ls: b.txt: No such file or directory 由于没有b.txt这个文件, 于是返回错误值, 这就是所谓的2输出 
a.txt 而这个就是所谓的1输出
```

复制

#### 再接着看:

```javascript
[[email protected] box]# ls a.txt b.txt 1>file.out 2>file.err
```

复制

执行后,没有任何返回值. 原因是, 返回值都重定向到相应的文件中了,而不再前端显示：

```javascript
[[email protected] box]# cat file.out 
a.txt 
[[email protected] box]# cat file.err 
ls: b.txt: No such file or directory
```

复制

一般来说, “1>” 通常可以省略成 “>”. 即可以把如上命令写成: ls a.txt b.txt >file.out 2>file.err

有了这些认识才能理解 “1>&2” 和 “2>&1”. 1>&2

正确返回值传递给2输出通道 &2表示2输出通道 如果此处错写成 1>2, 就表示把1输出重定向到文件2中. 2>&1

错误返回值传递给1输出通道, 同样&1表示1输出通道.

#### 举个例子.

```javascript
[[email protected] box]# ls a.txt b.txt 1>file.out 2>&1 
[[email protected] box]# cat file.out 
ls: b.txt: No such file or directory 
a.txt
```

复制

现在, 正确的输出和错误的输出都定向到了file.out这个文件中, 而不显示在前端.

补充下, 输出不只1和2, 还有其他的类型, 这两种只是最常用和最基本的.

**> 是重定向符，就是把前面输出的内容重定向到后面指定的位置，比如（例1）：**

```javascript
echo "一些内容" > filename.txt

上面例子会把 "一些内容" 写入到 filename.txt 文件中。
```

复制

**> 前是可以加数字来说明把什么内容重定向到文件中，默认是把标准输出重定向到文件中，所以下面这个例子和上面那个是一样的（例2）**：

```javascript
echo "一些内容" 1> filename.txt
```

复制

**如果是错误信息就不会输出到filename.txt（例3）：**

```javascript
$ ls nodir 1> filename.txt
$ ls: nodir: No such file or directory
```

复制

上面这个例子中nodir不存在，所以通过ls命令查询时错误信息会输出到 2(stderr)，但我们指定的是把1重定向到filename.txt，所以上面命令执行完后，filename.txt中是没有内容的。但是执行下面命令就会把错误信息写入到filename.txt中（例4）：

```javascript
$ ls nodir 2> filename.txt
$ cat filename.txt
$ ls: nodir: No such file or directory
```

复制

& 是一个描述符，如果1或2前不加&，会被当成一个普通文件。

1>&2 意思是把标准输出重定向到标准错误。

2>&1 意思是把标准错误输出重定向到标准输出。

&>filename 意思是把标准输出和标准错误输出都重定向到文件filename中。

我们再看一个例子（列5）：

```javascript
$ ls nodir 1> filename.txt 2>&1
$ cat filename.txt
$ ls: nodir: No such file or directory
```

复制

上面例子把 **标准输出** 重定向到文件 **filename.txt** 然后把 **标准错误** 重定向到 **标准输出**，所以最后的错误信息也通过标准输出写入到了文件中，比较例3，4，5，就能明白其作用。

下面是来自百度知道的内容，大家可以参考下：

```javascript
问：Linux重定向中 >&2 怎么理解？
问题补充：echo "aaaaaaaaaaaaaaaa" >&2 怎么理解？
```

复制

答：

```javascript
>&2 即 1>&2 也就是把结果输出到和标准错误一样；之前如果有定义标准错误重定向到某log文件，那么标准输出也重定向到这个log文件
如：ls 2>a1 >&2 （等同 ls >a1 2>&1）
把标准输出和标准错误都重定向到a1，终端上看不到任何信息。
```

参考：

[shell 1>&2 2>&1 &>filename重定向的含义和区别](https://cloud.tencent.com/developer/article/1139965#:~:text=2%3E%261%20%E6%84%8F%E6%80%9D%E6%98%AF%E6%8A%8A,%E5%AE%9A%E5%90%91%E5%88%B0%E6%96%87%E4%BB%B6filename%E4%B8%AD%E3%80%82&text=%E4%B8%8A%E9%9D%A2%E4%BE%8B%E5%AD%90%E6%8A%8A%E6%A0%87%E5%87%86%E8%BE%93%E5%87%BA,%E5%B0%B1%E8%83%BD%E6%98%8E%E7%99%BD%E5%85%B6%E4%BD%9C%E7%94%A8%E3%80%82)
