+++
title = "Linux用户管理命令详解"
date = "2018-01-22"
tags = [ "user", "命令详解" ]
categories = [
	"Linux-software",
	"Linux-Base",
   "Linux-server"
]

+++

#### 一、新建用户/组群 

##### A.用户 useradd

`useradd` 与` adduser` 命令都是创建新用户，但是不同之处是：

> `adduser` 会自动为创建的用户指定主目录、系统shell版本，会要求在创建时输入用户密码。

> `useradd`需要使用参数选项指定上述基本设置，如果不使用任何参数，则创建的用户无密码、无主目录、没有指定shell版本。

> 注意：CentOS7下效果一样。



* -d 指定主目录 ；
* -m 如果目录存在则不创建，但目录并不属于新 用户；果主目录不存在，则强制创建； -m 和-d一块使用。
* -s 指定用户登录时的shell版本。
* -M 不创建主目录。
* -g 指定用户主组群
* -G 指定用户附加组群 
* -r 创建系统用户

```shell
$ useradd -d "/home/test1" -m -s "/bin/nologin" -g admin_group -G www_group,ftp_group test1
```

##### B.组群 groupadd


*  -r 创建系统工作组，系统工作组的ID小于500.
*  -g 指定新组的ID

```shell
$ groupadd -g 666 linuxfan
```



#### 二、删除用户/组群

##### A. 用户 userdel


*  -f 强制删除用户，即使用户当前登录中。
*  -r 删除用户的同时，删除与用户相关的所有文件

```shell
$ userdel -fr test1
```

##### B.组群 groupdel

```shell
$ groupdel linuxfan
```

#### 三、修改用户/组群资料

##### A. 用户 usermod

usermod用于修改用户的基本信息。usermod命令不允许你改变正在线上的使用者帐号名称。
当usermod命令用来改变userID，必须确认这名user没在电脑上执行任何程序。

* -d  <登入目录> ：修改用户登入时的目录；
* -e  <有效期限> ：修改帐号的有效期限；
* -g   <群组>        ：修改用户所属的群组；
* -G   <群组>        ：改用户所属的附加群组；
* -l     <帐号名称> ：修改用户帐号名称；
* -L                         ：锁定用户密码，使密码无效；
* -s     <shell>      ：修改用户登入后所使用的shell；
* -u     <uid>        ：修改用户ID；
* -U                         ：解除密码锁定。

```shell
$ usermod -g admin -G www test1 ## 将用户test1的主组改为admin并加入www组中
$ usermod -l newuser1 newuser     ## 修改newuser的用户名为newuser1
```

##### B.组群 groupmod

> -n     修改组群名称

```shell
$ groupmod adm admin
```

#### 四、查看用户/组群

##### A.用户 id

```shell
$ id
$ id root
```

##### B.组群 groups

```shell
$ groups
```

#### 五、修改密码 passwd

* -d：删除密码，仅有系统管理者才能使用；
* -f：强制执行；
* -k：设置只有在密码过期失效后，方能更新；
* -l：锁住密码；
* -s：列出密码的相关信息，仅有系统管理者才能使用；
* -u：解开已上锁的帐号。

```shell
$ passwd text1
```

#### 六、关于shell

chsh命令用来更换登录系统时使用的shell。若不指定任何参数与用户名称，则chsh会以应答的方式进行设置。

* -s<shell 名称>或--shell<shell 名称>：更改系统预设的shell环境。；
* -l或--list-shells：列出目前系统可用的shell清单；
* -u或--help：在线帮助；
* -v或-version：显示版本信息。

```shell
[rocrocket@localhost ~]$ chsh -l
/bin/sh
/bin/bash
/sbin/nologin
/bin/zsh
```

使用chsh加选项-s就可以修改登录的shell了！你会发现你现在执行echo $SHELL后仍然输出为/bin/bash，这是因为你需要重启你的shell才完全投入到zsh怀抱中去。chsh -s其实修改的就是/etc/passwd文件里和你的用户名相对应的那一行。

```shell
[rocrocket@localhost ~]$ chsh -s /bin/zsh
Changing shell for rocrocket.
Password:
Shell changed.
```

把shell修改回/bin/bash：

```shell
[rocrocket@localhost ~]$ chsh -s /bin/bash
Changing shell for rocrocket.
Password:
Shell changed.
```



