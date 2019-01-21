+++
title = "VPS安装SS5和SoftEther代理"
date = "2017-08-16"
tags = [ "vps", "ss5", "softether"  ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

### 一、安装配置SockS Server 5服务端
虽然现在SockS5用得比较少了，而且SockS5都已经很久没有更新了，但是有的时候我们还真会用到，所以配置一下吧，有备无患嘛。
先还是下载SS5吧：
[官网：这里下载](http://ss5.sourceforge.net/) （点击左侧的Software在右侧的Server处进入下载地址）
Linux版本：CentOS6或者CentOS7均可。

<!-- more -->

#### 1、先从官网下载源码
```
# wget https://nchc.dl.sourceforge.net/project/ss5/ss5/3.8.9-8/ss5-3.8.9-8.tar.gz
```
#### 2、配置编译环境及安装编译SS5依赖组件

```
# yum -y install gcc automake make

# yum -y install pam-devel openldap-devel cyrus-sasl-devel openssl-devel
```
#### 3、解压、编译、安装
```
# tar xvf ss5-3.8.9-8.tar.gz -C /opt
# cd /opt/ss5-3.8.9-8
# ./configure && make && make install
```
#### 4、修改SS5配置文件
```
# nano /etc/opt/ss5/ss5.conf
```
找到并取消这两行前面的“#”：
```
auth    0.0.0.0/0               -              -
permit -        0.0.0.0/0       -       0.0.0.0/0       -       -       
```
默认的是：无用户认证。 
如果想要使用 username/password 用户认证方法，需要将上面两行修改成下面这样：
```
auth    0.0.0.0/0               -              u
permit -        0.0.0.0/0       -       0.0.0.0/0       -       -       
```
添加用户名及密码 /etc/opt/ss5/ss5.passwd，每行一个用户+密码（之间用空格）:
```
test1 12345
test2 56789
```
修改ss5启动的参数，自定义代理端口 /etc/sysconfig/ss5（如果不设置，默认是1080）.
#### 5、启动ss5
一定要在root用户目录下，执行:

```
# service ss5 start
```

#### 6、关闭ss5

```
# service ss5 stop
```

#### 7、将ss5加入开机自动启动（可选）

```
# chkconfig --add ss5
# chkconfig --level 345 ss5 on
```

### 二、安装配置 SoftEther VPN服务端

SoftEther是日本筑波大学开发的VPN，它实现了众多VPN协议（SSTP，L2TP over IPsec，Openvpn等）。
SoftEther源代码托管在Github：https://github.com/SoftEtherVPN/SoftEtherVPN/
这个VPN的优点是安装方便，中文配置，且可以win端直接配置服务器！！！

#### 1、安装依赖包

```
yum update
yum groupinstall "Development Tools"
yum install zlib-devel openssl-devel readline-devel ncurses-devel wget tar dnsmasq net-tools iptables-services system-config-firewall-tui
```

#### 2、安装SoftEther
下载最新SoftEther源码：http://www.softether-download.com/cn.aspx
（注意32位版本和64位版本！！！！重要的事情说三遍，不然make不了！！！！）

```
tar xzvf softether-vpnserver-v4.20-9608-x64-64bit.tar.gz -C /opt
cd /opt/vpnserver/
make     //或者 
./.install.sh    //两者效果一样
```
拉下来回答3个问题，全部选择1即可。

#### 3、启动/停止程序

```
./vpnserver start //启动程序
./vpnserver stop //停止程序
```

#### 4、配置SoftEther VPN Server

SoftEther的配置真是太爽啦，可以直接在终端命令配置：

```
cd /opt/vpnserver/
./vpncmd
```
然后按照提示即可配置完成。当然我们还有终极配置大法：
下载“SoftEther VPN Server管理器” [https://www.softether.org/](https://www.softether.org/)
配置详细内容网上一大堆，而且软件是全中文的，看着就会了，哈哈。
这里只提醒一下：

> 不要同时开启SecureNAT和Bridge ，否则会造成package loop，进而导致100% cpu usage。

#### 5、使用
可以下载客户端[http://www.vpngate.net/cn/](http://www.vpngate.net/cn/)
当然也可以不用下载，因为所有操作系统都自带VPN终端，连接方法参考这里：[http://www.vpngate.net/cn/howto.aspx](http://www.vpngate.net/cn/howto.aspx)


