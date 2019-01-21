+++
title = "使用frp进行内网穿透"
date = "2018-08-08"
tags = [ "frp", "内网穿透" ]
categories = ["Linux-Server", "Linux-Safe"]
+++

#### 一、简介

frp 是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp, http, https 协议。 
利用处于内网或防火墙后的机器，对外网环境提供 http 或 https 服务。
对于 http, https 服务支持基于域名的虚拟主机，支持自定义域名绑定，使多个域名可以共用一个80端口。
利用处于内网或防火墙后的机器，对外网环境提供 tcp 和 udp 服务，例如在家里通过 ssh 访问处于公司内网环境内的主机。

#### 二、官网及资源

- [frp的官网ReadMe](https://github.com/fatedier/frp/blob/master/README_zh.md)
- [frp的官方下载地址](https://github.com/fatedier/frp/releases)
- [基本使用视频教程](https://oengus.pipipan.com/fs/2577448-299452773)  [视频教程的文字版](https://www.vediotalk.com/?p=505)

#### 三、安装frp并配置开机启动

对下载的frp包进行解压操作后得到两个可执行文件 frps和frpc，分别为服务端和客户端。

将frps或者frpc复制到`/usr/local/bin`目录中，在`/etc/`下新建一个配置文件目录及配置文件frps或者frpc。

```shell
# tar zvxf frp.xxx.tar.gz .    #在当前目录解压frp压缩文件
# cp frps /usr/local/bin       #在服务端把frps文件复制到/usr/local/bin目录
# cp frpc /usr/local/bin       #在客户端把frpc文件复制到/usr/local/bin目录
# mkdir /etc/frps              #在服务端建立配置文件目录
# mkdir /etc/frpc              #在客户端建立配置文件目录
# cp frps.ini /etc/frps/       #在服务端把默认配置文件拷备到新建的配置文件目录
# cp frpc.ini /etc/frpc/       #在客户端把默认配置文件拷备到新建的配置文件目录
```

建立systemctl启动配置文件frps.service或者frpc.service

```shell
# vi frps.service
# vi frpc.service
```

把以下内容粘贴进去：（注意：如果是服务端请把ExecStart=/usr/local/bin/frps -c /etc/frpc/frps.ini替换原位置）

```shell
[Unit]
Description=frpc
After=network.target

[Service]
TimeoutStartSec=30
ExecStart=/usr/local/bin/frpc -c /etc/frpc/frpc.ini
ExecStop=/bin/kill $MAINPID

[Install]
WantedBy=multi-user.target
```

最后把启动文件拷贝到`/etc/systemd/system/`目录下。当下面第三步完成后即可用以下命令启停frp。

```shell
# systemctl start frps.service   
# systemctl stop frps.service
# systemctl restart frps.service
# systemctl status frps.service
```


#### 三、基本使用

##### 1. 服务端配置frps

```sh
[common]
allow_ports = 80,443,8000,6000-7000    # 配置允许frpc使用的端口
bind_port = 7000                       # frps与frpc通信需要的端口
vhost_http_port = 80                   # http使用的端口
vhost_https_port = 443                 # https使用的端口
dashboard_port = 8000                  # 服务端管理面板使用的端口    
dashboard_user = user1                 # 服务端管理面板使用的用户名 
dashboard_pwd = passwd                 # 服务端管理面板使用的密码      
privilege_token = abc123               # 确认frps和frpc连接的秘钥 
subdomain_host = abc.com               # 配置泛域名，这功能实在太强大了 超赞！！！ 
```

##### 2. 客户端配置frpc

```sh
[common]
server_addr = 123.123.123.123              # 服务端IP地址
server_port = 7000                         # frps与frpc通信需要的端口
privilege_token = abc123                   # 确认frps和frpc连接的秘钥 
login_fail_exit = false                    # 登录失败不退出，稍后继续连接 

[ssh]                                      # 服务名-可任意取，只要自己知道这是什么服务即可 
type = tcp                                 # 服务类型，可以是http、https、tcp、udp、xtcp、stcp等
local_ip = 127.0.0.1                       # 内网需要穿透的服务所在地址，可以是局域网内任何服务器 
local_port = 22                            # 内网需要穿透的服务的服务端口 
remote_port = 6022                         # 远端访问服务需要的端口，需要frps服务器打开防火墙端口。 

[qnap]  
type = http
local_ip = 10.0.0.88
local_port = 8080 
subdomain = qnap                           # 泛域名指定子域名，无需到域名面板中设置。超赞

[abc.com]  
type = http
local_ip = 127.0.0.1
local_port = 80
subdomain = www 

[T_web]  
type = http
local_ip = 127.0.0.1
local_port = 80
custom_domains = www.efg456.com            # 自定义域名访问  与 泛域名不同，且不冲突。赞

[Transmission]  
type = http
local_ip = 10.0.0.88
local_port = 9091
subdomain = pt 

[WD_NAS]  
type = http
local_ip = 10.0.0.111
local_port = 80
subdomain = wd
```

