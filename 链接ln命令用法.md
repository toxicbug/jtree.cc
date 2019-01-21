+++
title = "链接ln命令用法"
date = "2017-03-23"
tags = [ "ln", "ssh"  ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

### 一、链接 ln
链接有两种，一种被称为**硬链接**（Hard Link），另一种被称为符号链接（Symbolic Link）,也叫**软链接**。
建立硬链接时，链接文件和被链接文件必须位于同一个文件系统中，并且不能建立指向目录的硬链接。
而对符号链接，则不存在这个问题。默认情况下，ln产生硬链接。
它的功能是为某一个文件或目录在另外一个位置建立一个同步的链接，类似Windows下的超级链接。
命令如下：
```
ln -s 源文件 目标文件 
例如：
ln -s /home/XXXX/ ~/myfile  //将home目录中的XXXX目录映射到我的主目录中的myfile
```
删除链接
```
rm -rf  myfile   //注意不是rm -rf  myfile/ 
```
如下图，我们注意到文件属性最前面是 l ，说明它是一个链接。
![](http://p.xbay.top/snipaste_20170323_100139.png)
