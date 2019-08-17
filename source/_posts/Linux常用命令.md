---
title: Linux常用命令
date: 2019-06-09 16:34:38
tags:
  - Linux
description: 记录自己在学习以及工作中经常用到的Linux命令。
categories: Linux笔记
photos:
  - "https://i.loli.net/2019/06/09/5cfcc62f8a7d636528.jpg"
---

# 前言

这两周的工作基本都是与Linux有关，而自己并没有系统学习过Linux与操作系统课程，本篇博客就记录自己常用到的Linux命令，方便日后翻阅与补充。

# 常用命令

## 基本命令

- `cd [dirName]`命令，目录跳转，如：`cd /tmp/log`，需要注意的是绝对路径与相对路径

- `ls`命令，查看当前目录下的文件

- `pwd`命令，查看当前目录

- `ps [options] [--help]`命令，查看进程，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-ps.html)，常用的`ps`命令示例如下：
  - `ps –ef` 显示所有进程
  - `ps –ef|grep mysql` 显示与mysql相关的进程

- `kill [-s <信息名称或编号>][程序]`命令，杀死进程，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-kill.html)，常用的是在`ps`查看完进程pid后，使用`kill -9 123456`彻底杀死进程

- `cp [options] source dest`命令，复制文件，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-cp.html)，常用的是：`cp –r file /tmp` 复制file文件到tmp目录下

- `mv [options] source dest`命令，移动文件，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-mv.html)，常用的`mv`命令示例如下：
  - `mv file1 file1.bak` 将file1文件重命名为file1.bak
  - `mv info.log /tmp` 将info.log文件移动到tmp目录

- `rm [options] name...`命令，删除文件，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-rm.html)，常用的是：`rm a.txt` 删除a.txt文件

- `mkdir [-p] dirName`命令，新建目录，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-mkdir.html)，常用的是：`mkdir a` 建立一个名为a的子目录

- `tar [参数] file`命令，解压tar文件，常用的是；`tar -xvf file.tar -C /tmp`将file.tar压缩包解压到/tmp目录下

## 日志查看命令

除了`vi`与`vim`外，日志查看还可以用以下几个命令进行：

- `tail [参数] 文件`命令，各属性的解释不再赘述，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-tail.html)，常用的`tail`命令示例如下：
  - `tail -n 20 info.log` 显示文件最后20行
  - `tail -f info.log` 一直跟踪文件最后10行，当文件更新时，也会刷新变化，按`CTRL + C`结束

- `less [参数] 文件`命令，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-less.html)，常用的`less`命令示例如下：
  - `less info.log` 查看info.log，接着通过输入`/keyword`进行向下搜索或者输入`?keyword`进行向下搜索，通过`n`匹配下一个，`N`匹配上一个
  - `ps -ef |less` 查看进程信息，并分页展示

- `cat [参数] 文件`命令，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-cat.html)，常用的`cat`命令示例如下：
  - `cat -n info.log` 由1开始对所有输出的行数编号的方式查看info.log
  - `cat -n file1 > file2` 把file1的内容加上行号后输入到file2文件中

在我个人的线上日志查看以及异常排查当中，由于线上日志的量非常非常大，所以我通常会更多的使用`less`命令+搜索匹配的方式去定位异常。

## 查找文件命令

- `find path -option [-print] [-exec -ok command ] {} \`命令，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-find.html)，常用的`find`命令示例如下：
  - `find . -name info.log` 查找当前目录及其子目录下的info.log文件
  - `find . -name "*.log"` 查找当前目录及其子目录下的log后缀文件

- `ls`命令，一般用于查看当前目录下的文件，请参考[菜鸟教程](https://www.runoob.com/linux/linux-comm-find.html)，通过结合`grep`，可以实现搜索当前目录下的文件：`ls -l grep 'log'` 查找当前目录的log文件
