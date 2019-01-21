+++
title = "Aria2+AriaNg+caddy+frp完成内网离线下载功能"
date = "2018-08-18"
tags = [ "frp", "aria2" ]
categories = ["Linux-Server", "Linux-net"]
+++

​	aria2的介绍就不多说，全能型的命令行下载器。AriaNg是aria2的一个非常优秀的web前端。caddy是本人非常喜欢的相当轻量级的web服务器。frp大名顶顶的内网穿透工具。

​	首先是aria2的安装，当然基本上所有版本的linux都是一个命令完成,[caddy](https://jtree.cc/post/%E8%BD%BB%E9%87%8F%E7%BA%A7web%E6%9C%8D%E5%8A%A1%E5%99%A8caddy%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)和[frp](https://jtree.cc/post/%E4%BD%BF%E7%94%A8frp%E8%BF%9B%E8%A1%8C%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F/)本人之前均有详细介绍，不明白的话可以回去查看。

```shell
yaourt -Sy aria2c caddy 
```

​	我们还是先让aria2运行起来：

```shell
aria2c --enable-rpc --rpc-listen-all --rpc-allow-origin-all --rpc-secret=mima -c --dir /root/downloads
# --rpc-secret=mima mima为Aria2 RPC 秘钥，非常重要尽量设备复杂一点
# --dir /root/downloads  此项设置下载目录
# 如果能正常运行，则可以制作服务了
```

​	既然是制作服务，当然少不了service文件，本例中直接把上面的一长串命令拷入service中，无法运行，本人也百思不得其解。所以只能退而尔其次，制作shell脚本来辅助运行。效果其实是一样的。

```sh
#!/bin/bash
aria2c --enable-rpc --rpc-listen-all --rpc-allow-origin-all --rpc-secret=mima -c --dir /root/downloads
```

​	然后我们再把脚本放入`/etc/aria2/`目录中，如果没有（一般都没有）则建立该目录。现在我们再在`/etc/systemd/system/`目录中建立`aria2.service`文件：

![](http://p.jtree.cc/jtree.cc/aria2-service.png)

​	以下命令用来启动停止aria2的：

```shell
systemctl start aria2.service
systemctl status aria2.service
systemctl stop aria2.service
systemctl restart aria2.service
```

​	现在我们在让aria2的前端AriaNg运作起来。首先是:[下载AriaNg](https://github.com/mayswind/AriaNg/releases)

配置web服务器，因为前边有专门说明，这里就只给出配置：

![](http://p.jtree.cc/jtree.cc/aria2-caddy.png)

现在我们要做的就是打开6800商品（当然也可以是其它端口，不过需要在之前的脚本里面修改。同时利用frp来完善穿透内网：

![](http://p.jtree.cc/jtree.cc/aria2-frp.png)

​	经过以上几个步骤的处理，现在我们的内网下载机已经正式启动啦，什么bt呀、磁力链接呀通通来吧！