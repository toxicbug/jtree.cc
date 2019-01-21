+++
title = "查看分区格式和硬件查询命令"
date = "2019-01-16"
tags = ["df", "fdisk", "parted", "lsblk"]
categories = ["Linux-Server", "Linux-base"]
+++

#### 一、查询磁盘分区系统格式

我们在linux字符模式下经常需要查看磁盘的文件系统格式，当前有好几种方法可以查看，比如：

~~~shell
# df -T      可以查看已经挂载的分区的文件系统类型
# fdisk -l   可以显示出所有挂载和未挂载的分区，但不显示文件系统类型。
# parted -l  可以查看未挂载的文件系统类型，以及哪些分区尚未格式化。
# lsblk -f   可以查看未挂载的文件系统类型。
# file -s /dev/sda3
~~~

#### 二、查询CPU温度

* 查询树莓派3的核心温度

~~~shell
  # cat /sys/class/thermal/thermal_zone0/temp
  40622   返回值除1000，即为摄氏温度。
~~~

* 查询PC机的CPU温度
~~~shell
# apt-get install lm-sensors 　　# 安装
# sensors-detect　　　　　        # 侦测所有感测器　   
# sensors　　　　　　　　　　　　　　# 查看温度
~~~

#### 三、几个磁盘命令 lsblk、blkid、df、du

* lsblk(list block devices)能列出系统上所有的磁盘。

~~~shell
lsblk [-dfimpt] [device]
选项与参数：
-d ：仅列出磁盘本身，并不会列出该磁盘的分区数据
-f ：同时列出该磁盘内的文件系统名称
-i ：使用 ASCII 的线段输出，不要使用复杂的编码 （再某些环境下很有用）
-m ：同时输出该设备在 /dev 下面的权限数据 （rwx 的数据）
-p ：列出该设备的完整文件名！而不是仅列出最后的名字而已。
-t ：列出该磁盘设备的详细数据，包括磁盘伫列机制、预读写的数据量大小等
~~~

例子：

~~~shell
[root@192 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk 
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0   17G  0 part /
sdb      8:16   0   20G  0 disk 
sr0     11:0    1 1024M  0 rom

[root@192 ~]# lsblk -pf
NAME        FSTYPE   LABEL   UUID                         MOUNTPOINT
/dev/sda                                                      
├─/dev/sda1 xfs      c7c2cf29-2f94-4bb6-a334-7604c0c7c291  /boot
├─/dev/sda2 swap     2156bb3d-de11-4dd5-8599-70b16d3ef6b5  [SWAP]
└─/dev/sda3 xfs      7470460a-47a7-41a8-a4f0-7bcdb39b3506  /
/dev/sdb                                                      
/dev/sr0
~~~

* blkid能列出设备的uuid，当然lsblk -pf也能列出uuid。UUID是全域单一识别码 （universally unique identifier） ，Linux 会将系统内所有的设备都给予一个独一无二的识别 码， 这个识别码就可以拿来作为挂载或者是使用这个设备/文件系统之用

~~~shell
[root@192 ~]# blkid
/dev/sda1: UUID="c7c2cf29-2f94-4bb6-a334-7604c0c7c291" TYPE="xfs" 
/dev/sda2: UUID="2156bb3d-de11-4dd5-8599-70b16d3ef6b5" TYPE="swap" 
/dev/sda3: UUID="7470460a-47a7-41a8-a4f0-7bcdb39b3506" TYPE="xfs"
~~~

* df命令主要用来查看磁盘空间使用情况

~~~shell
-i			#列出inode情况
-T			#列出文件系统
-h			#human readable
#只是简单看下使用情况(常用)
df -h
#附带列出文件系统
df -hT
~~~

* du命令主要用来查看文件夹或文件的大小，也就是计算出文件占用了多少磁盘空间

~~~shell
-h, --human-readable		k,m,g单位来显示
-s,--summarize		    #列出总和 = --max-depth=0
-d,--max-depth=N	    #统计的深度
--exclude=''                #排除
~~~

例如：

~~~shell
du -a       #迭代显示所有文件以及目录占用空间大小

du -sh  dir | file         #显示dir或file总和大小(常用)
#列出当前目录各个文件夹及文件的大小(不迭代)(最常用)
#只显示1级目录
du -ah  -d 1
#显示当前1层文件及目录的大小
#排除.php结尾的文件
du -ah  --max-depth=1 --exclude='*.php'
~~~

