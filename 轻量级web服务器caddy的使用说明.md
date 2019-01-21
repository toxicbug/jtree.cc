+++
title = "轻量级web服务器caddy的使用说明"
date = "2018-03-27"
tags = [ "caddy", "web server", "blog" ]
categories = ["Linux-software", "Linux-Base", "Linux-server"]
+++

作为一个非专业人士，web对于我来说仅仅只需要能处理静态页面或者少量php即可，所以nginx对我来说就来难了，正好采用GO语言的caddy仅一个单文件，配置文件又简单到三两行代码，cpu和内存占用又极低，所以它才是我最理想的web服务器。

　　话虽如此，但是毕竟我不是专业人士，所以在caddy的配置道路上走得并不顺利，好在我没有放弃，不断找寻解决方法，终于完成了我的目标，本文权当是过程备忘录吧，以便下次使用可以快速找到答案。

<!-- more -->

#### 一、新建web专用帐号

为了安全，我们尽量专事专做，不用root帐号和其它主帐号。

```shell
# groupadd www-data
# useradd -M -g www-data www-data  
```

#### 二、安装与配置caddy

安装就按照 [官网](https://caddyserver.com/download) 一步步选择所需插件之后用脚本即可完成安装，接下来就是配置caddy。由于脚本安装完成之后，caddy的二进制文件默认在`/usr/local/bin/caddy/caddy`所以我们只需要创建配置文件和修改文件属性即可。

```shell
# mkdir /etc/caddy
# touch /etc/caddy/Caddyfile
# touch /etc/systemd/system/caddy.service     
// 官网上的这个文件是个大坑，怎么都无法启动
```

首先来配置一下启动文件(这是我的个人配置，把官网默认配置大部分内容删除，只保留了以下几个基本项）：

```bash
[Unit]
Description=Caddy HTTP/2 web server
Documentation=https://caddyserver.com/docs

[Service]
; User and group the process will run as.
User=www-data
Group=www-data

ExecStart=/usr/local/bin/caddy  -conf=/etc/caddy/Caddyfile -agree
ExecReload=/bin/kill -USR1 $MAINPID

; Use graceful shutdown with a reasonable timeout
KillMode=mixed
KillSignal=SIGQUIT
TimeoutStopSec=5s

[Install]
WantedBy=multi-user.target
```

这里需要简单说一下，如果启动不了，可以尝试：

```SHELL
# chmod u+s /usr/local/bin/caddy 
相当于给caddy一个SUID权限
```

#### 三、配置Caddyfile文件

　　首先要说的是Caddyfile文件是官方默认的配置文件，需要我们自己创建，文件名也可以根据自己需要而修改，只要记得在caddy.service文件里修改为对应的配置文件名即可。这里只给出我的配置，后面我会给出一篇文章，里面有详细的配置用法，偷个懒吧  [点这里](http://gtfix.com/2017/09/12/Caddy_config/)。

![](http://p.jtree.cc/jtree.cc/Ashampoo_Snap_2018.03.28_14h09m12s_001_.png)

#### 四、安装PHP

在centos7通过yum安装PHP7，首先在终端运行：
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
如果提示错误：
error: Failed dependencies:
epel-release >= 7 is needed by webtatic-release-7-3.noarch
需要先安装epel-release。
yum -y install epel-release 

```shell
# sudo yum install php72w-fpm
```

安装完成后只要在caddy配置文件中加入以下代码即可支持php：

```go
fastcgi / 127.0.0.1:9000 php {
    index index.php
}
```