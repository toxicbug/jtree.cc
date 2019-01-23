+++
title = "系统进程命令pstree"
date = "2018-03-03"
tags = [ "pstree", "tools", "base" ]
categories = ["Linux-Base", "Linux-Software"]
+++

　　先说一下什么是pstree, pstree以树结构显示进程(--display a tree of processes)
其优点在于您可以立即找出某进程的父进程：当您想要终止整个进程系列而且其中存在了许许多多的下属分支进程，您只需终止最上层的进程即可。您可能会需要使用 -p 选项显示每个进程的 PID，以及 -u 选项来显示启动该进程的用户名。一般来说，这一树结构比较长，您就需要这么执行 pstree：

```shell
$ sudo yum install psmisc
$ pstree -up
//    -u    显示启动该进程的用户名
//    -p    显示每个进程的 PID
```