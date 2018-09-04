+++
title = "小巧灵活的代理软件 Brook"
date = "2018-09-04"
tags = [ "代理", "VPN" ]
categories = [
	"Linux-Server",
    "Linux-Software"
]

+++

Brook是一个由 Go语言编写的跨平台代理软件，支持 **Linux/MacOS/Windows/Android/iOS 各个平台。**

同时因为 Go语言的特性，在操作使用上非常简单.

先在[官网](https://github.com/txthinking/brook)找到合适的版本，在终端使用wget即可下载，Brook为单文件，所以直接移动mv到`/usr/local/bin`目录即可。然后就是设置systemd自启动：

![](http://p.jtree.cc/jtree.cc/brook-set.png)

终端客户端使用以下连接：

```haskell
$ brook client -l 127.0.0.1:1080 -i 127.0.0.1 -s [server_address]:[port] -p [password]
```

