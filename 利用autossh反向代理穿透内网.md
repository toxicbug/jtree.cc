+++
title = "利用autossh反向代理穿透内网"
date = "2018-02-20"
tags = [ "autossh", "代理", "内网穿透" ]
categories = [
    "Linux-Base",
    "Linux-Software"
]
+++

　　这篇文章主要介绍了如何利用SSH 反向隧道穿透NAT，并演示了如何维持一条稳定的SSH 隧道。

　　假设有机器A 和B，A 有公网IP，B 位于NAT 之后并无可用的端口转发，现在想由A 主动向B 发起SSH 连接。由于B 在NAT 后端，无可用公网IP + 端口 这样一个组合，所以A 无法穿透NAT，这篇文章应对的就是这种情况。

首先有如下约定，因为很重要所以放在前面：

|   服务器    |   位置   |   地址    | 帐户  | ssh端口 | 一直运行sshd |
| :---------: | :------: | :-------: | :---: | :-----: | :----------: |
|   vps(A)    |   公网   |  a.site   | usera |   22    |      是      |
|  树莓派(B)  |  局域网  | localhost | userb |   22    |      是      |
| 其它电脑(C) | 任意网络 | localhost | userc |   22    |      否      |

#### 一、SSH反向隧道原理

　　这种手段实质上是由B 向A 主动地建立一个SSH 隧道，将A 的6766 端口转发到B 的22 端口上，只要这条隧道不关闭，这个转发就是有效的。有了这个端口转发，只需要访问A 的6766 端口反向连接B 即可。首先在B 上建立一个SSH 隧道，将A 的6766 端口转发到B 的22 端口上：

**树莓派(B)**

```shell
 $ ssh -p 22 -qngfNTR 6766:loaclhost usera@a.site
```

**VPS(A)**或者**其它电脑**

然后在A或者C 上利用6766 端口反向SSH 到B：

```shell
$ ssh -p 6766 userb@localhost   //A在本机上连接B
$ ssh -p 6766 userb@a.site      //其它电脑连接B
```
看吧，建立一个ssh隧道就这么容易。

#### 二、维持ssh隧道稳定

　　虽然ssh反向代理就是这么简单，然而不幸的是SSH 连接是会超时关闭的，如果连接关闭，隧道无法维持，那么A 就无法利用反向隧道穿透B 所在的NAT 了，为此我们需要一种方案来提供一条稳定的SSH 反向隧道。一个最简单的方法就是autossh，这个软件会在超时之后自动重新建立SSH 隧道，这样就解决了隧道的稳定性问题，你可以这样获得它：

```shell
$ pacman -S autossh
$ yum install autossh
$ apt-get install autossh
```

**树莓派(B)**
同样我们先从树莓派(B)开始向VPS(A)建立一条隧道，不同的是这条隧道会由autossh来维持：

```shell
$ autossh -p 22 -M 6777 -NR 6766:localhost usera@a.site  
# -M 参数指定的端口用来监听隧道的状态，与端口转发无关。
```
**VPS(A)**或者**其它电脑**
然后我们就可以从VPS(A)或者其它电脑(C)上进行连接树莓派(B)了：

```shell
$ ssh -p 6766 userb@localhost   //A在本机上连接B
$ ssh -p 6766 userb@a.site      //其它电脑连接B
```

#### 三、开洞

　　然而这又有了另外一个问题，如果B 重启隧道就会消失(比如断电、重启等)。那么需要有一种手段在B 每次启动时使用autossh 来建立SSH 隧道。很自然的一个想法就是做成服务，之后会给出在systemd 下的一种解决方案。

既然是开洞，那么这个洞自然是应该开在VPS(A)上，所以我们首先设置VPS(A)的配置文件，将GatewayPorts开关打开：

```shell
$ vim /etc/ssh/sshd_config
修改为：
GatewayPorts yes
然后重启sshd
$ systemctl restart sshd
```

**树莓派(B)**

在树莓派(B)上建立一个service服务，并专门建立一个用户来运行它，以确保安全。

先建立用户：

```shell
$ sudo useradd -m autovpn //新建一用户来只负责运行 systemctl start autossh.service
$ sudo passwd autovpn
```

为了起到全自动，不用人为输入新用户的密码，我们创建ssh密钥，并上传到VPS(A)上：

```shell
$ su - autovpn
$ ssh-keygen -t 'rsa' -C 'autovpn@pi'
$ ssh-copy-id usera@a.site  //当然也可以采用复制到VPS(A)上
```

现在建立service服务：

```shell
$ vim /lib/systemd/system/autossh.service
```

输入如下内容：


```shell
[Unit]
Description=Auto SSH Tunnel
After=network-online.target
[Service]
User=autovpn  //这是新建的用户专门运行本服务
Type=simple
ExecStart=/usr/bin/autossh -p 22 -M 6777 -NR 6766:localhost:22 usera@a.site -i /home/autovpn/.ssh/id_rsa  //注意id_rsa的用户目录是autovpn的哦
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=always
[Install]
WantedBy=multi-user.target
WantedBy=graphical.target
```

#### 四、畅游ssh隧道

启动服务并设置开机启动：

```shell
$ sudo systemctl enable autossh.service //开机启动
$ sudo systemctl start autossh.service //启动服务
```

剩下的就是连接到树莓派(B)了，方法同之前一样：

```shell
$ ssh -p 6766 userb@localhost   //A在本机上连接B
$ ssh -p 6766 userb@a.site      //其它电脑连接B
```

如果你对SSH 足够熟悉，你可以利用这条隧道做更多的事情，例如你可以在反向连接时指定动态端口转发：

```shell
$ ssh -p 6766 -qngfNTD 7677 userb@a.site
```

假设C 是你家中的电脑，A 是你的VPS，B 是你公司的电脑。如果你这样做了，那么为浏览器设置端口为7677 的sock5 本地（127.0.0.1）代理后，你就可以在家里的浏览器上看到公司内网的网页。

