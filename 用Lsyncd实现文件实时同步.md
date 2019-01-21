+++
title = "用Lsyncd实现文件实时同步"
date = "2018-03-30"
tags = [ "lsyncd", "备份", "同步" ]
categories = ["Linux-Base", "Linux-Software"]
+++

Lsyncd结合了inotify + rsync，Lsyncd监视本地目录树事件监视器接口（inotify或fsevents）。它聚合和组合事件几秒钟，然后生成一个（或多个）进程（es）来同步更改。

#### 一、安装Lsyncd

CentOS 7内置的源并没有包括Lsyncd，可以自行编译安装Lsyncd，不过更简单的办法是先安装epel源，就可以直接yum安装了。[官网地址](https://axkibe.github.io/lsyncd/)

```shell
# yum -y install epel-release
# yum -y install lsyncd
```

#### 二、配置Lsyncd

安装完成后配置文件位于`/etc/lsyncd.conf`，会看到如下的内容，其中`--`是注释符。这里给出两个我自己的常用配置，一个是本机同步，一个是远程同步。

- 本机同步设置

```lua
-- User configuration file for lsyncd.
-- Simple example for default rsync, but executing moves through on the target.
-- For more examples, see /usr/share/doc/lsyncd*/examples/
settings {
    logfile      ="/tmp/lsyncd.log",
    statusFile   ="/tmp/lsyncd.status",
    inotifyMode  = "CloseWrite",
    maxProcesses = 7,
    -- nodaemon =true,
    }
sync {
    default.rsync,
    source    = "/home/oengus/hugo/content/post",
    target    = "/mnt/NAS/temps/post",
    rsync     = {
        binary    = "/usr/bin/rsync",
        archive   = true,
        compress  = true,
        verbose   = true,
        perms     = true
        },
    }
```

- 远程同步设置

```lua
-- User configuration file for lsyncd.
-- Simple example for default rsync, but executing moves through on the target.
-- For more examples, see /usr/share/doc/lsyncd*/examples/
settings {
   logfile    = "/tmp/lsyncd.log",
   statusFile = "/tmp/lsyncd.status",
   insist = true,
   statusInterval = 10
}
sync {
   default.rsyncssh,
   source="/home/test1",
   host="192.168.1.200",
   targetdir="/home/test2",
   rsync = {
     archive = true,
     compress = false,
     whole_file = false
   },
   ssh = {
     port = 22
   }
}
```

继续输入命令`lsyncd -nodaemon /etc/lsyncd.conf`运行查看下是否有报错，如果有报错根据报错情况检查修改，如果没有报错直接退出后输入命令：`systemctl start lsyncd`启动lsyncd即可。这里给出systemd管理lsyncd的相关命令：

```shell
#启动
systemctl start lsyncd
#停止
systemctl stop lsyncd
#重启
systemctl restart lsyncd
#设置开机自动启动
systemctl enable lsyncd
```

#### 三、参数说明

- `settings 部分`,其实就是关于lsyncd工具自身的一些选项设置:

```shell
logfile 	  : 指定lsyncd工具本身运行所产生的日志文件存放位置
statusFile	  : 定义lsyncd监控目录状态文件的存放位置
statusInterval	  : 隔多少秒记录一次被监控目录的状态
nodaemon=true 	  : 默认是不启用守护模式的
inotifyMode	  : 指定要监控的事件,如,CloseWrite,Modify,CloseWrite or Modify
maxProcesses  	  : 指定同步时进程的最大个数
maxDelays	  : 当事件被命中累计多少次后才进行一次同步
```

- `sync 部分`主要用来定义同步时的一些设置,可以同时同步多个目录,只需要在该代码块中事先定义好多个sync即可：

```shell
default.rsync ：本地目录间同步，使用rsync，也可以达到使用ssh形式的远程rsync效果，或daemon方式连接远程rsyncd进程；
default.direct ：本地目录间同步，使用cp、rm等命令完成差异文件备份；
default.rsyncssh ：同步到远程主机目录，rsync的ssh模式，需要使用key来认证
source 同步的源目录，使用绝对路径。
target 定义目的地址.对应不同的模式有几种写法：
/tmp/dest ：本地目录同步，可用于direct和rsync模式
172.29.88.223:/tmp/dest ：同步到远程服务器目录，可用于rsync和rsyncssh模式，拼接的命令类似于/usr/bin/rsync -ltsd --delete --include-from=- --exclude=* SOURCE TARGET，剩下的就是rsync的内容了，比如指定username，免密码同步
172.29.88.223::module ：同步到远程服务器目录，用于rsync模式
init 这是一个优化选项，当init = false，只同步进程启动以后发生改动事件的文件，原有的目录即使有差异也不会同步。默认是true
delay 累计事件，等待rsync同步延时时间，默认15秒（最大累计到1000个不可合并的事件）。也就是15s内监控目录下发生的改动，会累积到一次rsync同步，避免过于频繁的同步。（可合并的意思是，15s内两次修改了同一文件，最后只同步最新的文件）
excludeFrom 排除选项，后面指定排除的列表文件，如excludeFrom = "/etc/lsyncd.exclude"，如果是简单的排除，可以使用exclude = LIST。
这里的排除规则写法与原生rsync有点不同，更为简单：
监控路径里的任何部分匹配到一个文本，都会被排除，例如/bin/foo/bar可以匹配规则foo
如果规则以斜线/开头，则从头开始要匹配全部
如果规则以/结尾，则要匹配监控路径的末尾
?匹配任何字符，但不包括/
*匹配0或多个字符，但不包括/
**匹配0或多个字符，可以是/
delete: 为了保持target与souce完全同步，Lsyncd默认会delete = true来允许同步删除。它除了false，还有startup、running值
```

- `rsync 部分`rsync工具自身的一些设置选项:

```shell
compress   = true    : 压缩后再同步
bwlimit	   = 200    : 限速同步,当你不想占满带宽时
archive	   = true    : 归档模式同步
perms      = true    : 保留文件原有属性同步
```

