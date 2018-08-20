---
title: 用chfs搭建简单的文件分享服务
categories: [操作系统,Linux]
date: 2017-12-19
tags: 
  - Linux
  - 命令详解
---

#### 一、简介

chfs是是一个免费的、HTTP协议的文件共享服务器，使用浏览器可以快速访问。它具有以下特点：

- 单个文件，整个软件只有一个可执行程序，无配置文件等其他文件
- 部署简单
- 跨平台运行，支持Windows(xp sp2及其以上版本)和Linux
- 界面简洁，简单易用
- 支持扫码下载和手机端访问，手机与电脑之间共享文件非常方便

与其他常用文件共享方式（如FTP，飞秋）相比，访问者只需要打开浏览器直接访问地址即可，无需额外下载专用客户端。在个人使用以及共享给他人的场景中非常方便快捷。

#### 二、下载

[官网下载](http://iscute.cn/chfs)

#### 三、安装使用

本文仅以Linux下配置为例。

```
# wget xxx                                                //先下载chfs
# cp chfs /usr/local/bin/                          //复制到/usr/local/bin/ 目录
# useradd  chfs                                        //新建用户chfs，以便安全管理
# chown chfs:chfs /usr/local/bin/chfs  
# chmod 500 /usr/local/bin/chfs 
# mkdir /home/chfs/www/
# chown chfs:chfs /home/chfs/www/
# chmod -R 700 /home/chfs/www/
# screen -S chfs
# su - chfs
$ chfs --port=8989 --path="/home/chfs/www/"   //指定端口与根目录 

Ctrl+A +D 退出screen。

```

#### 四、使用感受

chfs使用方便，单文件，无太多参数，但是缺点也不少：无权限管理，尤其是谁都可以上传。不管怎么说它也是一款优秀的文件共享服务器，小巧快速。

---

作者版本更新，新增了配置文件和访问控制功能，详情查看官网。以下是配置文件格式：

```shell
#---------------------------------------
# 请注意：
#     1，如果不存在键或对应值为空，则不影响对应的配置
#     2，配置项的值，语法如同其对应的命令行参数
#---------------------------------------
# 监听端口
port=
# 共享根目录
# 注意：带空格的目录须用引号包住，如 path="c:\a uply name\folder"
path=
# IP地址过滤
allow=
# 账户控制规则
rule=
```

示例：

```shell
//都使用默认参数，共享目录为程序运行目录，监听端口号为80
chfs

//共享目录为D盘，监听端口号为8080
chfs --path="d:/" --port=8080

//共享目录为"/home/jack/myshared files"，监听端口号为80
chfs --path="/home/jack/myshared files"

//白名单模式，允许192.168.1.2-192.168.1.100以及192.168.1.200进行访问
chfs --allow="192.168.1.2-192.168.1.100,192.168.1.200"

//黑名单模式，禁止192.168.1.2-192.168.1.100以及192.168.1.200进行访问
chfs --allow="not(192.168.1.2-192.168.1.100,192.168.1.200)"

//匿名用户具有只读权限（默认情况下匿名用户具有读写权限）
//账户ceshizu，密码为ceshizu123，对根目录的权限为只读，但对test目录具有读写权限
//账户yanfazu，密码为yanfazu123，对根目录的权限为只读，但对yanfa目录具有读写权限
chfs --rule="::r|ceshizu:ceshizu123:r:test:rw|yanfazu:yanfazu123:r:yanfa:rw"

//匿名用户什么权限都没有（默认情况下匿名用户具有读写权限）
//账户admin，密码为admin123，具有读写权限
//账户zhangsan，密码为zhangsan123，对根目录的权限为不可读写，但对zhangsanfiles目录具有读写权限
chfs --rule="::|admin:admin123:rw|zhangsan:zhangsan123::zhangsanfiles:rw"

//通过配置文件进行配置，该文件可以不存在，待以后需要更改配置时使用
chfs --file="d:\chfs\chfs.ini"
```

