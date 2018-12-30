+++
title = "VPS上安装Resilio-Sync"
date = "2017-03-22"
tags = [ "vps", "resillio sync", "ssh"  ]
categories = [
​	"Linux-software",
​	 "Linux-server"
]
+++

### 一、安装
#### 1.添加源
Create file **/etc/yum.repos.d/resilio-sync.repo** with the following content to register Resilio repository:

```
[resilio-sync]
name=Resilio Sync
baseurl=http://linux-packages.resilio.com/resilio-sync/rpm/$basearch
enabled=1
gpgcheck=1
```

 <!-- more -->

#### 2.添加公钥
Add public key:
```
rpm --import https://linux-packages.resilio.com/resilio-sync/key.asc
```
#### 3.安装
Install Sync:
```
yum update
yum install resilio-sync
```
### 二.配置 **[官方教程](https://help.getsync.com/hc/en-us/articles/206178924-Installing-Sync-package-on-Linux)**

#### 1.自动启动
Enable sync service automatic startup as user rslsync:
```
systemctl enable resilio-sync
```
Systemctl can be also run with the following arguments: start, stop, enable, disable, status

#### 2.商品配置
用Htop命令查看运行情况，并查看配置文件在哪？
![](http://p.xbay.top/snipaste_20170322_175233.png)
```
# nano /etc/resilio-sync/config.json
```
![](http://p.xbay.top/snipaste_20170322_175155.png)
#### 3.重启Resilio-Sync
```
systemctl restart resilio-sync
```

#### 4. 被墙后用SS+Proxifier达到连同的设置

首先是架设SS服务器，不用多说。

利用Proxifier来设置分流代理

* 先设置代理服务器

  ![](http://p.jtree.cc/blog/20181205131855.png?imageslim)

* 再设置代理规则（主要是173.244.217.42和209.95.56.60这两个IP）

  ![](http://p.jtree.cc/blog/20181205131918.png?imageslim)


