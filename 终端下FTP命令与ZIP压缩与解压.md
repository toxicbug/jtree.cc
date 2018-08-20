+++
title = "终端下FTP命令与ZIP、7z压缩与解压"
date = "2017-03-10"
tags = [ "ftp", "zip", "7z" ]
categories = [
	"Linux-base",
	"Linux-software",
   "Linux-server"
]
+++

很多时候我们还是需要在Linux终端下上传、下载文件，所以FTP命令还是需要的，当然还有ZIP文件的压缩与解压，主要是大多数桌面压缩还是winzip格式，所以也有必要备忘一下。
![](http://p.jtree.cc/jtree.cc/FTP.jpg)

<!-- more -->

## FTP命令
如果没有安装ftp端的话可以`yum install ftp`
### 一、建立连接
```
ftp domain.com 或者
ftp
open domain.com
```
### 二、用户名密码
```
Name: anonymous
Password:
```
### 三、FTP端操作
```
ls
mkdir
cd
...其它文件操作命令
```
### 四、本地操作命令
```
lcd ~/download //进入本地~、download目录
```
### 五、上传与下载文件
```
put xxx.tar //把本地xxx.tar文件上传到服务器
mput *.zip  //把本地目录所有zip文件上传到服务器
get xxx.tar.gz //把服务器上xxx.tar.gz下载到本地目录
mget *.zip   //把服务器上所有zip文件下载到本地
```
### 六、退出FTP
```
quit
exit
bye
```

## ZIP压缩与解压
### 安装
```
yum install zip unzip p7zip
```

### 压缩ZIP包

```
我想把一个文件abc.txt和一个目录xyz压缩成为yasuo.zip：
zip -r yasuo.zip abc.txt xyz/
```
### 解压ZIP
```
我下载了一个yasuo.zip文件，想解压缩：
unzip yasuo.zip
```
