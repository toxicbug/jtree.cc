+++
title = "使用systemd挂载文件系统"
date = "2018-04-07"
tags = [ "mount", "systemd", ]
categories = [
	"Linux-Base"
]
+++

为什么使用systemd来挂载文件系统而不使用fstab？使用fstab的时候如果设备被移除会导致系统启动失败，无法进入系统（只能进入临时修复系统），systemd则没有这个问题。


```shell
# vim /etc/systemd/system/mnt-data.mount
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

开机启动:

```shell
# systemctl enable mnt-data.mount
```

注意：
1. 如果挂载nfs的话需要安装nfs客户端
2. 文件名称必须要与你挂载的路径一致，把路径里的「/」换成「 -」，例如本例要挂的目录为/mnt/data，则文件名称必须为mnt-data.mount。

```shell
# yum install -y nfs-utils
# apt-get install -y nfs-common
```

