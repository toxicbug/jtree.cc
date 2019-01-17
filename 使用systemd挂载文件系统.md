+++
title = "使用systemd挂载文件系统"
date = "2018-04-07"
tags = [ "mount", "systemd", ]
categories = [
	"Linux-Base"
]
+++

为什么使用systemd来挂载文件系统而不使用fstab？使用fstab的时候如果设备被移除会导致系统启动失败，无法进入系统（只能进入临时修复系统），systemd则没有这个问题。

#### 一、挂载本地磁盘


```shell
# vim /etc/systemd/system/xxx-xxx.mount
```

以下为配置内容:

```shell
[Unit]
Description = mount disk

[Mount]
What = /dev/sdb4
Where = /mnt/data
Type = ext4
Options = defaults

[Install]
WantedBy = local-fs.target
```

#### 二、挂载网络文件系统

```shell
# vim /etc/systemd/system/xxx-xxx-xxx.mount
```

```shell
[Unit]
Description = Mount NFS disk

[Mount]
What = nfs.example.com:/export/scratch
Where = /mnt/data/nfs
Type = nfs
Options = defaults

[Install]
WantedBy = multi-user.target
```

#### 三、自动挂载

```shell
# vim /etc/systemd/system/xxx-xxx-xxx.automount
```

```shell
[Unit]
Description = Auto Mount NFS disk

[Automount]
Where = /mnt/data/nfs

[Install]
WantedBy = multi-user.target
```

#### 四、开启关停

```shell
# systemctl start xxx-xxx-xxx.mount
# systemctl stop xxx-xxx-xxx.mount
# systemctl disable xxx-xxx-xxx.mount
# systemctl statu xxx-xxx-xxx.mount
如果使用automount，则必须使用：
# systemctl enable xxx-xxx-xxx.automount
没用使用automount，则使用：
# systemctl enable xxx-xxx-xxx.mount
```

```shell
  # systemctl is-enabled mnt-scratch.mount
  disabled
  # systemctl is-enabled mnt-scratch.automount                               
  enabled
  # systemctl start mnt-scratch.automount                                     
  # ls /mnt/scratch >/dev/null
  # systemctl status mnt-scratch.automount
  ● mnt-scratch.automount - Automount Scratch
     Loaded: loaded (/etc/systemd/system/mnt-scratch.automount; enabled; vendor preset: disabled)
     Active: active (running) since Mon 2016-04-18 10:49:04 CEST; 4h 33min ago
      Where: /mnt/scratch

  Apr 18 10:49:04 oat systemd[1]: Set up automount Automount Scratch.
  Apr 18 10:49:14 oat systemd[1]: mnt-scratch.automount: Got automount request for /mnt/scratch, triggered by 20266 (zsh)
  # systemctl status mnt-scratch.mount
  ● mnt-scratch.mount - Scratch
     Loaded: loaded (/proc/self/mountinfo; disabled; vendor preset: disabled)
     Active: active (mounted) since Mon 2016-04-18 10:49:16 CEST; 4h 33min ago
      Where: /mnt/scratch
       What: nfs.example.com:/export/scratch

  Apr 18 10:49:14 oat systemd[1]: Mounting Scratch...
  Apr 18 10:49:16 oat systemd[1]: Mounted Scratch.
```

#### 五、重要事项：

**（以下内容非常重要，可能直接导致挂载失败。）**

1. mount文件名称**必须**要与你**挂载路径一致**，把路径里的**「/」**换成**「 -」**，例如本例要挂的目录为`/mnt/data`，则文件名称必须为`mnt-data.mount`。可以多层目录如`xxx-xxx-xxx.mount`。
2. 如果挂载nfs的话需要安装nfs客户端：

```shell
# yum install -y nfs-utils
# apt-get install -y nfs-common
```
3. 找好很久很久才找到的内容，也正是这篇文章，才使得我真正用上systemd的挂载系统。[《Automatic mounts with systemd》](https://blog.tomecek.net/post/automount-with-systemd/)

