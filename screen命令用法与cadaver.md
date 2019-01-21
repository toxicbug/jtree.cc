+++
title = "screen命令用法与cadaver"
date = "2017-12-19"
tags = [ "screen", "cadaver", "ssh" ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

## screen命令用法

### 一、简介
作为linux服务器管理员,经常要使用ssh登陆到远程linux机器上做一些耗时的操作。

也许你遇到过使用telnet或SSH远程登录linux,运行一些程序。如果这些程序需要运行很长时间(几个小时)，而程序运行过程中出现网络故障，或者客户机故障，这时候客户机与远程服务器的链接将终端，并且远程服务器没有正常结束的命令将被迫终止。

又比如你SSH到主机上后，开始批量的scp命令，如果这个ssh线程断线了，scp进程就中断了。在远程服务器上正在运行某些耗时的作业，但是工作还没做完快要下班了，退出的话就会中断操作了，如何才好呢？

我们利用screen命令可以很好的解决这个问题。实现在断开SSH的情况下,在服务器上继续执行程序。


### 二、Screen功能说明

简单来说，Screen是一个可以在多个进程之间多路复用一个物理终端的窗口管理器,这意味着你能够使用一个单一的终端窗口运行多终端的应用。Screen中有会话的概念，用户可以在一个screen会话中创建多个screen窗口，在每一个screen窗口中就像操作一个真实的telnet/SSH连接窗口那样。

<!-- more -->

### 三、Screen命令语法


```
screen [-AmRvx -ls -wipe][-d <作业名称>][-h <行数>][-r <作业名称>][-s ][-S <作业名称>]

Screen命令参数：
-A -[r|R]          将所有的视窗都调整为目前终端机的大小。
-c filename        用指定的filename文件替代screen的配置文件’.screenrc’.
-d [pid.tty.host]  断开screen进程(使用该命令时，screen的状态一定要是Attached，也就是说有用户连在screen里)。一般进程的名字是以pid.tty.host这种形式表示(用screen -list命令可以看出状态)。
-D [pid.tty.host]  与-d命令实现一样的功能，区别就是如果执行成功，会踢掉原来在screen里的用户并让他logout。
-h <行数> 　       指定视窗的缓冲区行数。

-ls或–list        显示目前所有的screen作业。
-m                    即使目前已在作业中的screen作业，仍强制建立新的screen作业。
-p number or name  预先选择一个窗口。
-r [pid.tty.host]  恢复离线的screen进程，如果有多个断开的进程，需要指定[pid.tty.host]
-R                      先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
-s shell             指定建立新视窗时，所要执行的shell。
-S <作业名称>  指定screen作业的名称。(用来替代[pid.tty.host]的命名方式,可以简化操作).
-v                     显示版本信息。
-wipe                检查目前所有的screen作业，并删除已经无法使用的screen作业。
-x                     恢复之前离线的screen作业。

```

### 四、Screen实现后台运行程序的简单步骤

* 要进行某项操作时，先使用命令创建一个Screen:

```
[linux@user~]$ screen -S test1

```

* 接着就可以在里面进行操作了，如果你的任务还没完成就要走开的话，使用命令保留Screen：

```
[linux@user~]$ Ctrl+a+d                    #按Ctrl+a，然后再按d即可保留Screen
[detached]                                 #这时会显示出这个提示，说明已经保留好Screen了
```

* 如果你工作完成的话，就直接输入:

```
[linux@user~]$ exit                        #这样就表示成功退出了
[screen is terminating]
```

* 如果你上一次保留了Screen，可以使用命令查看：

```
[linux@user~]$ screen -ls
There is a screen on:
9649.test1   (Detached)
```

* 恢复Screen，使用命令：

```
[linux@user~]$ screen -r test1 (or 9649)
```

### 五、Screen命令中常用快捷键


```
Ctrl+a c ：创建窗口

Ctrl+a w ：窗口列表

Ctrl+a n ：下一个窗口

Ctrl+a p ：上一个窗口

Ctrl+a 0-9 ：在第0个窗口和第9个窗口之间切换

Ctrl+a K(大写) ：关闭当前窗口，并且切换到下一个窗口（当退出最后一个窗口时，该终端自动终止，并且退回到原始shell状态）

exit ：关闭当前窗口，并且切换到下一个窗口（当退出最后一个窗口时，该终端自动终止，并且退回到原始shell状态）

Ctrl+a d ：退出当前终端，返回加载screen前的shell命令状态

```

Linux的screen命令挺不错，在服务器上做点什么费时的工作就不用愁了！

## 字符模式下的webdav客户端

```
# yum install -y cadaver

cadaver https://pubdav.ctfile.com

输入用户名和密码后就可以使用get 、put等命令
```


