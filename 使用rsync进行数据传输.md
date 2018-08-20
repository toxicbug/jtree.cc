+++
title = "使用rsync进行数据传输"
date = "2018-08-07"
tags = [ "rsync", "备份", "base" ]
categories = [
	"Linux-software",
	"Linux-Base",
    "Linux-server"
]
+++

`rsync`和`scp`一样，是用于服务器间数据传输的工具。相比于scp主要有以下优点：

- 传输前会进行检测，跳过最终修改时间戳和文件大小一致的文件
- 传输前会对数据进行压缩
- 支持断点续传

基本用法：

```shell
rsync -arvzP --bwlimit=100 'abc@192.168.1.1:/home/data/*' /data/back/

重要参数说明：

-P 等同于 --partial       支持断点续传
--progress                   显示过程
--exclude='*shell*'      排除不需要传的文件
--rsh='ssh -p 8005'     不是使用默认端口
--bwlimit=100             带宽限制，单位为KBPS
```

