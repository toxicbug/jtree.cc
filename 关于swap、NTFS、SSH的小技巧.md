+++
title = "关于swap、NTFS、SSH的小技巧"
date = "2018-01-19"
tags = [ "swap", "NTFS", "ssh" ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

### 一、 为小内存VPS增加swap

由于我选购的主机内存比较低，为降低系统完全卡死的概率，需要挂载一个虚拟内存。虚拟内存的大小，一般不要超过当前内存大小，笔者选购的云主机是1GB内存，所以设置1GB虚拟内存。

<!-- more -->

```shell
# 创建虚拟内存文件
$ dd if=/dev/zero of=/mnt/swap bs=1024 count=1048576
# 格式化虚拟内存文件
$ mkswap /mnt/swap
# 挂载虚拟内存
$ swapon /mnt/swap
# 检查虚拟内存挂载成功
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           992M        476M         77M        3.9M        438M        345M
Swap:          1.0G          0B        1.0G
# 配置重启电脑后自动挂载虚拟内存
# 添加以下内容到/ect/fstab文件
# /mnt/swap swap swap defaults 0 0
$ vi /etc/fstab
```

### 二、 CentOS7下挂载NTFS磁盘

CentOS默认不支持NTFS的，需要单独安装ntfs-3g这个程序后，才可以对ntfs进行挂载与读写。

ntfs-3g存在于EPEL源中，所以我们还要添加EPEL源，顺便把基础源也换阿里的，速度快一些：

##### 1.备份原来的yum源：

```shell
$ sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak 
```

##### 2.安装阿里基础源：

```shell
$ sudo wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

##### 3.添加阿里EPEL源：

EPEL（http://fedoraproject.org/wiki/EPEL）是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。装上 EPEL后，可以像在 Fedora 上一样，可以通过 yum install package-name，安装更多软件。

```shell
$ sudo wget -P /etc/yum.repos.d/ http://mirrors.aliyun.com/repo/epel-7.repo
```

##### 4.清理缓存并生成新的缓存

```shell
$ sudo yum clean all  
$ sudo yum makecache  
```

##### 5.安装ntfs-3g

```shell
$ yum install ntfs-3g
```



### 三、关于sshd的增补内容

##### 1.使用sshkey登录后，除了要禁用密码登录外，还要禁用root登录

```shell
 # vi /etc/ssh/sshd_config
    把
    PermitRootLogin yes
    改为
    PermitRootLogin no
```



##### 2.ssh私钥登陆失败:server refused our key原因

如果确定key没有问题，那极有可能是.ssh目录/authorized_keys文件权限不对造成的

> .ssh目录权限 600
>
> authorized_keys文件权限 400

##### 3.解决SSH延迟问题

Linux下的ssh 服务器一般用的都是OpenSSH，可是发现有些时候通过ssh连接服务器时总会有大概10秒钟左右的延迟。解决方法如下：

>  修改服务器上的/etc/ssh/sshd_config文件，注意是sshd_config，不是ssh_config。

>  将其中的UseDNS一行的注释去掉，改为：

>  UseDNS no

默认情况下，UseDNS选项是打开的，当客户端连接ssh服务器时，ssh服务器会根据客户端的ip地址RTR反向查询客户端的hostname，然后再根据hostname进行DNS解析，得到ip地址，验证与原客户端的ip地址是否一致。这是一种防欺诈的手段，但是我们做实验的服务器都在内网中，客户端也在内网中，没有RTR记录打开这个选项也没有意义，只会浪费时间。