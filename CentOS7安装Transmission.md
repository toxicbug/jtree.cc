+++
title = "CentOS7安装Transmission"
date = "2018-02-20"
tags = [ "Transmission", "下载" ]
categories = ["Linux-Server", "Linux-Software"]
+++

　　虽然当前很多人购买的Linux VPS主机，但是大部分都局限在一般的上网或者是架设简单的网站用途。不过，Linux VPS主机的实际使用功能往往不止这些，我们可以用来特定项目环境的搭建，有的是用来搭建远程客户端刷单，也有的用来外汇项目，更有用来安装Transmission用于下载用途的。

　　不过这里需要注意的是，我们在下载项目的时候，要确定vps商家是否支持，而且对于特定版权内容的建议谨慎，因为海外商家对于特定的视频文件是有版权的，正如书生说的下载国外或者新的视频文件要谨慎。

<!-- more -->

#### 一、安装

首先安装EPEL Repository：

```shell
$ yum install epel-release
$ yum -y update
```

再安装Transmission：

```shell
$ yum install transmission-cli transmission-common transmission-daemon
```

安装完成后先运行一次，再停止运行：

```shell
$ systemctl start transmission-daemon.service
$ systemctl stop transmission-daemon.service
```
如果运行出现如下错误：

```shell
$ systemctl start nfs-server.service
Failed to start nfs-server.service: The name org.freedesktop.PolicyKit1 was not provided by any .service files
See system logs and 'systemctl status nfs-server.service' for details.
```

错误内容如果含有`org.freedesktop.PolicyKit1`,则有可能是polkit出问题，我的解决办法是：

```shell
$ yum install polkit
$ systemctl start polkit
```



#### 二、配置

打开配置文件：

```shell
$ vim /var/lib/transmission/.config/transmission-daemon/settings.json
```

按如下配置：

```shell
"rpc-authentication-required": true,
"rpc-enabled": true,
"rpc-password": "mypassword",
"rpc-username": "mysuperlogin",
"rpc-whitelist-enabled": false,
"rpc-whitelist": "0.0.0.0",
```

#### 三、运行

重新运行transmission：

```shell
$ systemctl start transmission-daemon.service
```

然后打开http://yourdomain.com:9091 配置好下载目录及速度等即可使用。