+++
title = "Linux终端进程管理工具"
date = "2017-01-15"
tags = [ "htop", "xkill", "pgrep"  ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

​	Linux终端有一系列有用的命令。它们可以显示正在运行的进程、杀死进程和改变进程的优先级。本文列举了一些经典传统的命令和一些有用新颖的命令。本文提到的命令会实现某个单一功能。其它命令，例如htop，会在命令的上层提供一个友好的界面供用户使用。

## top
top命令是一个常用的查看系统资源使用情况和查看占用系统资源最多的进程的命令。top以列形式显示所有的进程，占最多CPU资源的进程会显示在最上面。
要退出top或者htop，可以使用键盘快捷键Ctrl-C。这个键盘快捷键通常会终止目前在终端上运行的进程。

<!-- more -->

## htop -- 相当好用的 进程管理工具
**有些CentOS版本不带epel的需要先安装epel**
```
yum install epel-release
yum install htop
```
htop命令是top的改进版。默认情况下，大多数Linux发行版本都没有安装htop。可以自行apt-get或者yum安装。htop命令显示的信息与top相同，但它的界面更人性化。你可以使用键盘箭头键选择进程和采取某些动作，例如杀死进程或者改变它们的优先级。

---

## ps
ps命令可以列出正在运行的进程。以下命令列出所有在你系统上运行的命令：
```
ps -A 
```
这个命令列出的信息也许太多，不方便阅读。你可以使用less命令对输出进行管道，这样你就可以按你的速度滚动阅读：
```
ps -A | less 
```
当你阅读完后，可以按q退出。
你也可以使用grep来对输出做管道，这样可以不需要使用其它命令就能搜索出某个进程。以下命令会搜索Firefox进程：
```
ps -A | grep firefox 
```
## pstree
pstree命令也可以显示进程信息。它以树的形式显示进程。例如，你的x系统和图形环境会出现在产生树状进程的显示管理器的下面。

## kill
kill命令可以根据进程ID来杀死进程。你可以使用ps -A，top,或者grep命令获取到进程ID。
```
kill pid 
```
从技术层面来讲，kill命令可以发送任何信号给一个进程。你可以使用kill -KILL或者kill -9来杀死顽固的进程.

## pgrep
给定一个搜索关键词，pgrep命令会返回所有匹配这个关键词的进程ID。例如，你可以使用以下命令寻找Firefox的PID:
```
pgrep firefox 
```
你也可以将这个命令与kill命令结合起来杀死一个特定的进程。但是，使用pkill或者killall会更简单。
## pkill & killall
pkill和killall命令可以根据进程的名字杀死一个进程。使用以下任一方法都可以杀死Firefox进程：
```
pkill firefox 
killall firefox 
```
## renice
renice命令用来改变进程的nice值。nice值代表进程的优先级。-19的nice值是非常高的优先级，相反，19是非常低的优先级。0是默认的优先级。
运行renice命令需要使用进程的ID。以下命令可以让某个进程以非常低的优先级运行。
```
renice 19 pid 
```
你可以把pregrep和renice结合起来使用，如上图所示。
如果你想把进程的优先级调高，那么你需要使用root权限。在Ubuntu系统，使用sudo获取root权限：
```
renice -19 # 
```
## xkill
xkill命令是一个可以轻易杀死图形程度的命令。运行它之后，你的光标会变成x符号。点击相应的图形程序的窗口就可以杀死该程序。如果你中途要放弃操作，你可以点击鼠标右键取消。
你不一定要在终端运行这个命令——你可以在图形桌面上按Alt-F2，输入xkill然后按回车键来运行它。我们已经将xkill和热键绑定，这样杀死进程就更容易了。

