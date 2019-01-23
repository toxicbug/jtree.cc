+++
title = "用chfs搭建简单的文件分享服务"
date = "2017-12-19"
tags = [ "chfs", "webdav", "file" ]
categories = ["Linux-Base", "Linux-Software"]
+++

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


#----------------- 账户控制规则 -------------------
# 注意：该键值可以同时存在多个，你可以将每个用户的访问规则写成一个rule，这样比较清晰，如：
#     rule=::
#     rule=root:123456:RW
#     rule=readonlyuser:123456:R
rule=


# 用户操作日志存放目录，默认为空
# 如果赋值为空，表示禁用日志
log=


# 网页标题
html.title=


# 网页顶部的公告板。可以是文字，也可以是HTML标签，此时，需要适用一对``(反单引号，通过键盘左上角的ESC键下面的那个键输出)来包住所有HTML标签。几个例子：
#     1,html.notice=内部资料，请勿传播
#     2,html.notice=`<img src="https://mat1.gtimg.com/pingjs/ext2020/qqindex2018/dist/img/qq_logo_2x.png" width="100%"/>`
#     3,html.notice=`<div style="background:black;color:white"><p>目录说明：</p><ul>一期工程：一期工程资料目录</ul><ul>二期工程：二期工程资料目录</ul></div>`
html.notice=


# 是否启用图片预览(网页中显示图片文件的缩略图)，true表示开启，false为关闭。默认开启
image.preview=


#-------------- 设置生效后启用HTTPS -------------
# 指定certificate文件
ssl.cert=
# 指定private key文件
ssl.key=
```

参数说明：

参数说明：

| **help:**    | 显示帮助信息                                                 |
| ------------ | ------------------------------------------------------------ |
| **path:**    | 你要共享的根目录，默认为程序运行目录。比如：共享"d:\http_shared_root"目录，运行参数："chfs --path="d:\http_shared_root""。**注意：如果路径带有空格，则需要将整个路径用引号包住！** |
| **port:**    | 程序使用的端口号，默认为80                                   |
| **allow:**   | IP地址过滤，可使用白名单模式或黑名单模式                     |
| **rule:**    | 账户及访问权限，允许一个账户多点登陆，默认情况下匿名用户具有读写权限，其语法为：  **RULEITEM1[\|RULEITEM2\|RULEITEM3...]**  每个RULEITEM代表一个账户信息及其访问权限，多个RULEITEM则用'\|'进行分割，RULEITEM的语法为：  **USER:PWD:MASK[:DIR:MASK...]**  每个项由“:”来分隔，前三个项是必须的，分别对应：账户名、账户密码、共享目录根目录的访问权限。后面的可选的项，必须成对出现，用来设定根目录下面的子级目录的访问权限。一些规定：  * 对于匿名用户，前两个项都为空 * 访问权限分为四种：""(不可访问)，"R"(只读)，"W"(读写)，"D"(写+删除)。读权限指的是下载，写权限指上传、新建等操作，删除权限是在写权限的基础上加上删除权限。 * 各项的值应避免出现空白键，':'及'\|'（目录名除外） |
| **log:**     | 用户操作日志存放目录，默认是程序所在目录下的logs中。禁用日志功能只需将其赋值为空即可。 |
| **file:**    | 配置文件，该文件可配置上述配置项，语法相同，如果配置有效则覆盖对应配置项。另外，一些功能需要通过配置文件进行配置，比如页面自定义和SSL证书设置。[下载配置文件模板](http://iscute.cn/asset/chfs.ini) |
| **version:** | 显示程序版本号                                               |

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

这里给出systemd启动文件配置：

~~~shell
$ sudo systemctl cat chfs
# /etc/systemd/system/chfs.service
[Unit]
Description=chfs
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/chfs --file=/etc/chfs/chfs.ini
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process

[Install]
WantedBy=multi-user.target
~~~

