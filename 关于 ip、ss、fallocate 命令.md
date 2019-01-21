+++
title = "关于 ip、ss、fallocate 命令"
date = "2018-01-22"
tags = [ "IP", "ss", "fallocate" ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

　　ip命令和ss命令是iproute2网络管理工具包里面的两个非常重要和好用的命令，同时也是ifconfig命令的接替者;
　　fallocate命令则是被人遗忘的非常不错的创建大文件的命令。
　　
<!-- more -->

#### 一、ip命令

##### 1.查看IP地址、网络信息

```shell
$ ip addr show
或者
$ ip a s
查询某网上信息
$ ip a s enp3s0
```

##### 2.启用/禁用网上

```sh
启用网卡
# ip link set enp3s0 up 
或者
# ip l s enp3s0 up
禁用网卡
# ip link set enp3s0 down
或者
# ip l s enp3s0 down
```

#### 二、ss命令

查看网络端口

```shell
等同于 netstat -nultp
$ ss
或者
$ ss -l
```

#### 三、 fallocate命令

　　Linux 创建一个大文件，也许很多人都是想到使用 dd 命令，但是 fallocate 命令更加简单好用。

##### 1.创建1G的大文件

```shell
$ fallocate -l 1G /home/bigfile
等同于
$ dd if=/dev/zero of=233ABC bs=1G seek=1 count=0
```

##### 2.制作虚拟内存

```shell
创建2G的文件
# fallocate -l 2G /mnt/swap
更改文件权限，让是swap只能由root读写
# chmod 600 /mnt/swap
格式化交换分区
# mkswap /mnt/swap
启用交换空间
# swapon /mnt/swap
写入fatab
/mnt/swap   swap   swap   sw   0 0
```

