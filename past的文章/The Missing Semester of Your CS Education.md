---
title: 计算机教育中缺失的一课（小记）
date: 2023-09-03 21:53:23
tags: [课程笔记]
categories: [课程笔记,计算机基础]
mathjax: true
---
# The Missing Semester of Your CS Education 计算机教育中缺失的一课

计算机导论的补充，介绍了工作中常用的几个工具，Shell，Git，Vim等，适合初学者了解。

[课程链接](https://missing-semester-cn.github.io/)

<!--more -->

# 1.shell 初步

shell 基于空格分割命令并进行解析，然后执行第一个单词代表的程序，并将后续的单词作为程序可以访问的参数。

``` shell
date //显示时间:2023-09-03 22:00:31
echo hello //hello
```

单、双引号，转义符号`\`可以在参数中加空格

```shell
"My Photos"
My\Photos
```

### 目录分隔：

- Linux 和 macOS 上使用 `/` 分割
- Windows上是 `\`
- `.` 表示的是当前目录
-  `..` 表示上级目录

```shell
pwd //获取当前路径
cd \home //home子目录
cd .. //返回上级目录
```

```shell
ls //当前目录下包含的文件
```

`rwx` 分别代表了文件所有者（`missing`），用户组（`users`） 以及其他所有人具有的权限：

- `w`：只有文件所有者可以修改
- `x`：用户需要具备该文件夹以及其父文件夹的“搜索”权限
- `r`：用户必须对该文件夹具备读权限

```shell
mv //重命名或移动文件
cp // 拷贝文件
mkdir //新建文件夹

man //查询指令的工作方式
例：
man ls
```

### 重定向：

- `< file` 输出流
-  `> file`输入流

```shell
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello
missing:~$ cat < hello.txt
hello
missing:~$ cat < hello.txt > hello2.txt
missing:~$ cat hello2.txt
hello
```

 `|` 操作符允许我们将一个程序的输出和另外一个程序的输入连接起来

```shell
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2
219
```

### sudo

root user（根用户）可以自由创建、读取、更新和删除系统中的任何文件，通常在我们并不会以根用户的身份直接登录系统。

 `sudo` 命令让您可以以 su（super user 或 root 的简写）的身份执行一些操作。

**PS**：只有根用户才能向 `sysfs` 文件写入内容（Windows 和 macOS 没有这个文件）

# 其他

[课程资料](https://missing-semester-cn.github.io/)中文版足够阅读，本文截至。
