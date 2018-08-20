+++
title = "命令行下BT种子与磁力链接下载的正确姿势"
date = "2018-04-25"
tags = [ "tget", "aria2" ]
categories = [
	"Linux-software",
	"Linux-Base"
]
+++

wget是linux下常用的命令行下载工具，是Linux用户是必不可少的工具，尤其对于网络管理员，经常要下载一些软件。而tget和aria2是命令行下可以用于BT种子和磁力链接的相当好用的下载工具。
　　tget使用Nodejs开发，基于 torrent-stream ，代码量很少。
　　tget源代码： https://github.com/jeffjose/tget 类似的项目： https://github.com/mafintosh/peerflix 

#### 一、安装Node.js和NPM

```bash
CentOS 7
# curl -sL https://rpm.nodesource.com/setup | bash -
# yum install -y nodejs
Ubuntu
# curl -sL https://deb.nodesource.com/setup | bash - 
# apt-get install -y nodejs  
ArchLinux
# pacman -Sy nodejs npm
```

#### 二、安装tget和aria2

```bash
tget
# npm install -g t-get
aria2
# yum install epel-release
# yum install -y aria2
# apt-get install aria2
# pacman -Sy aria2
```

#### 三、一言不合就开下

* BT种子

```bash
tget
$ wget http://releases.ubuntu.com/16.04/ubuntu-16.04.1-desktop-amd64.iso.torrent
$ tget ubuntu-16.04.1-desktop-amd64.iso.torrent 
aria2
$ aria2c ubuntu-16.04.1-desktop-amd64.iso.torrent 
```
* Metalink

```bash
tget
$ tget 'magnet:?xt=urn:btih:248D0A1CD08284299DE78D5C1ED359BB46717D8C'
aria2
$ aria2c 'magnet:?xt=urn:btih:248D0A1CD08284299DE78D5C1ED359BB46717D8C'
```