+++
title = "visudo命令简要配置"
date = "2017-09-30"
tags = [ "visudo", "sudo", "ssh" ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

​	你可以自由的活在一个没有terminal的Windows的世界，但是当你爱上Linux、Mac时，你将离不开terminal，离不开sudo。
当我们安装完CentOS后的第一件事情就应该是设置一个普通用户，然后就是一大堆的sudo command...
而visudo就是它的开始，所以首先我们得明白visudo是什么？
```bash
# visudo                               //它其实等同于
# vi /etc/sudoers
```
<!-- more -->

### 一、最简单的设置

```
## Allow root to run any commands anywhere
...省略
     root        ALL=(ALL)       ALL
     test1       ALL=(ALL)       ALL         //新增一行，注意格式对齐
     test2       ALL=(ALL)       NOPASSWD:ALL
     test3       ALL=(ALL)       NOPASSWD:/usr/bin/yum
...省略
```

> 第一个字段：root为能使用sudo命令的用户；
> 第二个字段：第一个ALL为允许使用sudo的主机，第二个括号里的ALL为使用sudo后以什么身份(目的用户身份）来执行命令；
> 第三个字：ALL为以sudo命令允许执行的命令；
> 上列解释： test   ALL=(root) /usr/sbin/useradd 表示允许test用户从任何主机登录，以root的身份执行/usr/sbin/useradd命令。

### 二、sudo深入配置

其实我也没搞明白啦，给个配置表，自己深入研究：

```
## Sudoers allows particular users to run various commands as  
## the root user, without needing the root password.  
## 该文件允许特定用户像root用户一样使用各种各样的命令，而不需要root用户的密码  
##  
## Examples are provided at the bottom of the file for collections  
## of related commands, which can then be delegated out to particular  
## users or groups.  
## 在文件的底部提供了很多相关命令的示例以供选择，这些示例都可以被特定用户或  
## 用户组所使用   
##  
## This file must be edited with the 'visudo' command.  
## 该文件必须使用"visudo"命令编辑  
  
## Host Aliases  
## Groups of machines. You may prefer to use hostnames (perhaps using   
## wildcards for entire domains) or IP addresses instead.  
## 对于一组服务器，你可能会更喜欢使用主机名（可能是全域名的通配符）  
## 、或IP地址，这时可以配置主机别名  
# Host_Alias     FILESERVERS = fs1, fs2  
# Host_Alias     MAILSERVERS = smtp, smtp2  
  
## User Aliases  
## These aren't often necessary, as you can use regular groups  
## (ie, from files, LDAP, NIS, etc) in this file - just use %groupname   
## rather than USERALIAS  
## 这并不很常用，因为你可以通过使用组来代替一组用户的别名  
# User_Alias ADMINS = jsmith, mikem  
  
## Command Aliases  
## These are groups of related commands...  
## 指定一系列相互关联的命令（当然可以是一个）的别名，通过赋予该别名sudo权限，  
## 可以通过sudo调用所有别名包含的命令，下面是一些示例  
  
## Networking 网络操作相关命令别名  
# Cmnd_Alias NETWORKING = /sbin/route, /sbin/ifconfig, /bin/ping, /sbin/dhclient  
, /usr/bin/net, /sbin/iptables, /usr/bin/rfcomm, /usr/bin/wvdial, /sbin/iwconfig  
, /sbin/mii-tool  
  
## Installation and management of software 软件安装管理相关命令别名  
# Cmnd_Alias SOFTWARE = /bin/rpm, /usr/bin/up2date, /usr/bin/yum  
  
## Services 服务相关命令别名  
# Cmnd_Alias SERVICES = /sbin/service, /sbin/chkconfig  
  
## Updating the locate database 本地数据库升级命令别名  
# Cmnd_Alias LOCATE = /usr/bin/updatedb  
  
## Storage 磁盘操作相关命令别名  
# Cmnd_Alias STORAGE = /sbin/fdisk, /sbin/sfdisk, /sbin/parted, /sbin/partprobe  
, /bin/mount, /bin/umount  
  
## Delegating permissions 代理权限相关命令别名  
# Cmnd_Alias DELEGATING = /usr/sbin/visudo, /bin/chown, /bin/chmod, /bin/chgrp   
  
## Processes 进程相关命令别名  
# Cmnd_Alias PROCESSES = /bin/nice, /bin/kill, /usr/bin/kill, /usr/bin/killall  
  
## Drivers 驱动命令别名  
# Cmnd_Alias DRIVERS = /sbin/modprobe  
  
# Defaults specification  
  
#  
# Disable "ssh hostname sudo <cmd>", because it will show the password in clear.   
#         You have to run "ssh -t hostname sudo <cmd>".  
# 一些环境变量的相关配置，具体情况可见man soduers  
Defaults    requiretty  
  
Defaults    env_reset  
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS"  
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"  
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"  
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"  
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"  
  
Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin  
  
## Next comes the main part: which users can run what software on   
## which machines (the sudoers file can be shared between multiple  
## systems).  
## 下面是规则配置：什么用户在哪台服务器上可以执行哪些命令（sudoers文件可以在多个系统上共享）  
## Syntax(语法):  
##  
##  user    MACHINE=COMMANDS 用户 登录的主机=（可以变换的身份） 可以执行的命令  
##  
## The COMMANDS section may have other options added to it.  
## 命令部分可以附带一些其它的选项  
##  
## Allow root to run any commands anywhere   
## 允许root用户执行任意路径下的任意命令  
root    ALL=(ALL)   ALL  
  
## Allows members of the 'sys' group to run networking, software,   
## service management apps and more.  
## 允许sys中户组中的用户使用NETWORKING等所有别名中配置的命令  
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE  
, DRIVERS  
  
## Allows people in group wheel to run all commands  
## 允许wheel用户组中的用户执行所有命令  
%wheel  ALL=(ALL)   ALL  
  
## Same thing without a password  
## 允许wheel用户组中的用户在不输入该用户的密码的情况下使用所有命令  
# %wheel    ALL=(ALL)   NOPASSWD: ALL  
  
## Allows members of the users group to mount and unmount the   
## cdrom as root  
## 允许users用户组中的用户像root用户一样使用mount、unmount、chrom命令  
# %users  ALL=/sbin/mount /mnt/cdrom, /sbin/umount /mnt/cdrom  
  
## Allows members of the users group to shutdown this system  
## 允许users用户组中的用户关闭localhost这台服务器  
# %users  localhost=/sbin/shutdown -h now  
  
## Read drop-in files from /etc/sudoers.d (the # here does not mean a comment)  
## 读取放置在/etc/sudoers.d/文件夹中的文件（此处的#不意味着这是一个声明）  
#includedir /etc/sudoers.d 
```
