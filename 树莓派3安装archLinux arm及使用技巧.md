+++
title = "树莓派3安装archLinux arm及使用技巧"
date = "2018-05-07"
tags = [ "Raspberry Pi", "ArchLinux", "arm" ]
categories = [
	"Linux-Raspberry Pi",
	"Linux-Base",
    "Linux-server"
]
+++
#### 一、树莓派3安装ArchLinux

首先，你需要根据树莓派的类型下载对应的安装包。比如 [树莓派3](http://mirrors.ustc.edu.cn/archlinuxarm/os/ArchLinuxARM-rpi-3-latest.tar.gz) 。
格式化SD卡，并分两个区。

```bash
# lsblk
# fdisk /dev/sdX   
```

然后按以下操作进行分区

```bash
输入 p 并回车，这将会列出所有分区，查看现在分区
输入 o 并回车，这将会删除所有分区 
输入 p 并回车，这将会列出所有分区，此时应该没有任何分区 
输入 n 并回车，创建新分区，引导分区 
输入 p 并回车，新分区为主分区 
输入 1 并回车，分区序号是1 按键盘回车，默认初始扇区 
输入 +100M 并回车，设置终止扇区 
输入 t 并回车，再输入 c 并回车，设置该分区文件系统格式为Fat32 
输入 n 并回车，创建新分区，根分区 
输入 p 并回车，新分区为主分区 
输入 2 并回车，分区序号是2 按键盘回车，默认初始扇区 按键盘回车，默认终止扇区 
输入 w 并回车，写入设置
推荐100M空间作为boot分区，分区文件系统Fat32，再把剩余的空间创建一个分区，分区文件系统为Ext4，作为root,然后格式化保存即可 
  
```

一个boot分区。格式化为fat32并设定类型为W95 FAT32 (LBA)。
一个根分区。格式化为ext4。`（建议使用命令mkfs.ext4 -O ^has_journal /dev/sdX2禁用日志，减轻SD卡的负载）`

```shell
# mkfs.vfat /dev/sdX1 
# mkfs.ext4 /dev/sdX2
```

挂载并将镜像文件里面boot目录内部的文件写到/boot分区，将其他文件写入根分区。

```shell
# mkdir /mnt/{boot, root} 
# mount /dev/sdX1 /mnt/boot 
# mount /dev/sdX2 /mnt/root
# bsdtar -xpf ArchLinuxARM-rpi-3-latest.tar.gz -C /mnt/root 
如果bsdtar无法解压，也可直接用tar解压
# tar -zxvf ArchLinuxARM-rpi-3-latest.tar.gz -C /mnt/root 
# sync
# mv /mnt/root/boot/* /mnt/boot
```

运行snyc，将缓存强制写入SD卡。

顺便修改软件源：

```bash
# vi root/etc/pacman.d/mirrorlist
以下为源内容：
#清华 
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/arch/repo 
# 中科大 
Server = http://mirrors.ustc.edu.cn/archlinuxarm/arch/repo 
#成都电子科大 
Server = http://mirrors.stuhome.net/archlinuxarm/arch/repo
```

卸载SD卡，然后我们就可以将SD卡插入树莓派运行了。

#### 二、登录Raspberry Pi

没有显示器和键盘的自己找路由器里面的树莓派IP ssh登录

> 默认ssh用户：alarm

> 默认ssh密码：alarm

> 默认root用户密码也是root

```shell
pacman-key --init
pacman-key --populate archlinuxarm
```

#### 三、安装常用包及yaourt

```shell
# pacman -Syu
# pacman -S wget git curl vim
安装基本包
# pacman -S base-devel
```

编译yaourt相关依赖 [参照文件](http://doku.ben00it.fr/dokuwiki/doku.php?id=linux:raspberry:yaourt)

```shell
# pacman -S gcc cmake fakeroot pkg-config
```

安装package-query

```shell
# cd /tmp
# git clone https://aur.archlinux.org/package-query-git.git
# cd package-query-git
# makepkg -Acs
# pacman -U package-query-*.xz
```

安装yaourt

```shell
# cd /tmp/
# git clone https://aur.archlinux.org/yaourt.git
# makepkg -Acs
# pacman -U yaourt*.tar.xz
```

#### 四、其它

使用Arch最大的感受就是不要用除了pacman以及yaourt以外的任何包管理系统（比如python的pip），否则会给日常的系统升级带来麻烦。
此外Raspbain以及Arch都非常的稳定，我自己日常使用时没有遇到过什么太大的问题。

#### 五、pacman与yaourt命令详解

##### pacman命令


Pacman 是一个命令行工具，这意味着当你执行下面的命令时，必须在终端或控制台中进行。

```yaml
1、更新系统
在 Arch Linux 中，使用一条命令即可对整个系统进行更新：
pacman -Syu
如果你已经使用 pacman -Sy 将本地的包数据库与远程的仓库进行了同步，也可以只执行：
pacman -Su
2、安装包
pacman -S 包名 例如，执行 pacman -S firefox 将安装 Firefox。你也可以同时安装多个包，只需以空格分隔包名即可。
pacman -Sy 包名 与上面命令不同的是，该命令将在同步包数据库后再执行安装。
pacman -Sv 包名 在显示一些操作信息后执行安装。
pacman -U 安装本地包，其扩展名为 pkg.tar.gz。
3、删除包
pacman -R 包名 该命令将只删除包，不包含该包的依赖。
pacman -Rs 包名 在删除包的同时，也将删除其依赖。（一般用这个）
pacman -Rd 包名 在删除包时不检查依赖。
4、搜索包
pacman -Ss 关键字 这将搜索含关键字的包。
pacman -Qi 包名 查看有关包的信息。
pacman -Ql 包名 列出该包的文件。
5、其他用法
pacman -Sw 包名 只下载包，不安装。
pacman -Sc Pacman 下载的包文件位于 /var/cache/pacman/pkg/ 目录。该命令将清理未安装的包文件。
pacman -Scc 清理所有的缓存文件。
6、降级系统
pacman -Syyuu
你必须重视这一点：虽然在大多数情况下这不会引起问题，但是这种可能性还是存在，即降级一个包或几个包将会引起级联传播的失败并会将你的系统处于不一致的状态
```

##### yaourt命令

```bash
yaourt -S ： 从aur 安装包
yaourt -Ss :  查询
yaourt -Sc ：清除旧的包/缓存
yaourt -R ：删除
yaourt -Su : 安装更新包
yaourt -Sy ：获取数据库
yaourt -Cd ：清除数据库
yaourt -Syu --aur ：升级aur的包
yaourt -Si ：列出包的信息
```

#### 六、参考

[颓废风-树莓派3下arch Linux arm 一些优化](https://www.sysblz.com/article/13694117.html)  、 [ifconfiger-树莓派安装Arch Linux](https://ifconfiger.com/articles/Install-Arch-Linux-on-Raspberry-Pi) 、[archlinux官方手册](https://wiki.archlinux.org/index.php?title=Yaourt_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)&oldid=235625) 

#### 七、其它aur包助手

	考虑到官方给出yaourt最近开发缓慢且可能停止继续开发，而给出yaourt为危险程序的警告。我们不得不寻找其它其它aur包助手。

##### aurman

[aurman](https://github.com/polygamma/aurman)  是最好的AUR帮手之一，可以替代Yaourt。它与pacman的语法几乎相似，支持所有pacman操作。您可以搜索AUR，解决依赖关系，在包构建之前检查PKGBUILD内容等。

##### 安装aurman

```shell
$ git clone https://aur.archlinux.org/aurman.git
$ cd aurman
$ makepkg -si
```

##### 使用aurman

```shell
$ aurman -S <package-name>
```

##### yay

[yay](https://github.com/Jguer/yay)是用Go编写的下一个最好的AUR助手，目的是提供一个pacman接口，用户输入最少，像搜索一样，几乎没有依赖。

- yay提供AUR表完成并从ABS或AUR下载PKGBUILD。
- 支持缩小搜索范围，不支持PKGBUILD。
- 二进制文件没有pacman的附加依赖项。
- 提供高级依赖项解算器，并在构建过程结束时删除make依赖项。
- 在/etc/pacman.conf文件中启用Color选项时支持彩色输出。
- 它可以仅支持AUR包或仅支持repo包。

##### 安装yay

```shell
$ git clone https://aur.archlinux.org/yay.git
$ cd yay
$ makepkg -si
```

##### 使用yay

```shell
$ yay -S <package-name>
```

