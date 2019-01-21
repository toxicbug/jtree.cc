+++
title = "screen的替代者,终端复用者Tmux使用梳理"
date = "2018-04-21"
tags = [ "终端", "tmux" ]
categories = ["Linux-software", "Linux-Base"]
+++

　　Tmux是一个优秀的终端复用软件，类似GNU Screen，但来自于OpenBSD，采用BSD授权。使用它最直观的好处就是，通过一个终端登录远程主机并运行tmux后，在其中可以开启多个控制台而无需再“浪费”多余的终端来连接这台远程主机；是BSD实现的Screen替代品，相对于Screen，它更加先进：支持屏幕切分，而且具备丰富的命令行参数，使其可以灵活、动态的进行各种布局和操作。下面就Tmux的使用做一梳理：

#### 一、Tmux功能：

-  提供了强劲的、易于使用的命令行界面。
-  可横向和纵向分割窗口。
-  窗格可以自由移动和调整大小，或直接利用四个预设布局之一。
-  支持 UTF-8 编码及 256 色终端。
-  可在多个缓冲区进行复制和粘贴。
-  可通过交互式菜单来选择窗口、会话及客户端。
-  支持跨窗口搜索。
-  支持自动及手动锁定窗口。

#### 二、Tmux安装

```bash
$ sudo apt-get install tmux           //用于ubuntu
$ sudo yum install -y tmux           //用于CentOS7及以上版本
MacOS安装
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"   //安装 Homebrew
$ brew install tmux      // 安装tmux
```

#### 三、Tmux的使用

　　安装完成后输入命令tmux即可打开软件，界面十分简单，类似一个下方带有状态栏的终端控制台；但根据tmux的定义，在开启了tmux服务器后，会首先创建一个会话，而这个会话则会首先创建一个窗口，其中仅包含一个面板；也就是说，这里看到的所谓终端控制台应该称作tmux的一个面板，虽然其使用方法与终端控制台完全相同。直接使用`tmux`命令即可打开tmux：

![](http://p.jtree.cc/Ashampoo_Snap_2018.04.21_09h41m41s_001_.png)

#### 四、Tmux的快捷键使用说明：

Tmux的操作对象共有三个，系统操作、窗口操作、面板操作，均需要进入控制台后方可生效，进入控制台的方法是：`Ctrl+b`

##### 1、系统操作

- `?`--列出所有快捷键

- `ctrl+b    d`脱离当前会话；这样可以暂时返回Shell界面，输入`tmux att`能够重新进入之前的会话;注意：直接`ctrl+d`是退出当前窗口（如果多开tmux窗口的情况下）或者退出tmux程序。

- `ctrl+b    D`同时开启了多个会话时,暂时返回Shell界面

- `ctrl+b    s`选择并切换会话；在同时开启了多个会话时使用


##### 2、窗口操作

- `ctrl+b    c`创建新窗口
- `ctrl+b   &`关闭当前窗口
- `ctrl+b    数字键`切换至指定窗口
- `ctrl+b    w`通过窗口列表切换窗口
- `ctrl+b    f`在所有窗口中查找指定文本

##### 3、面板操作 

- `ctrl+b    "`将当前面板平分为上下两块
- `ctrl+b    %`将当前面板平分为左右两块
- `ctrl+b    x`关闭当前面板
- `ctrl+b    !`将当前面板置于新窗口；即新建一个窗口，仅包含当前面板
- `ctrl+b    方向键`用方向键选择面板
- `ctrl+b    t`显示时钟。然后按enter键后就会恢复到shell终端状态