+++
title = "另外一个强大的shell----fish"
date = "2018-09-12"
tags = ["shell", "ohmyfish"]
categories = [
​	"Linux-Server",
​    "Linux-base"
]

+++

　　之前介绍过一个非常强大好用的shell---[ohmyzsh](https://jtree.cc/post/oh-my-zsh%E4%BD%93%E9%AA%8C%E5%B0%8F%E8%AE%B0/),而且本人也一直在使用，最近老是在论坛的回复中看到fish的存在，我那颗不安分的心又开始躁动了。。。

#### 一、首先给出几个官网

[fish官网](https://fishshell.com/)、[oh-my-fish官网](https://github.com/oh-my-fish/oh-my-fish)、[Git官网](https://git-scm.com/)

#### 二、安装git

　　由于oh-my-fish安装需要git版本大于1.9.3，而centos通过yum安装git版本是1.8，所以我们只能手动安装高版本git，而且还是编译安装。不过其实也容易。如果之前通过yum安装过git请一定要先卸载。

```shell
# yum remove git
```

　　根据[Git官网](https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)的安装方法先安装必要依赖工具和库文件,之后从官网下载最新源码包：

```shell
# yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel   //安装依赖包和库文件
# yum install -y bzip2-devel ncurses-devel gcc perl-ExtUtils-MakeMaker package
# wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.9.5.tar.gz  //官网下载最新版
# tar -zxf git-1.7.2.2.tar.gz    //解压
# cd git-1.7.2.2
# make prefix=/usr/local all  
# sudo make prefix=/usr/local install
```

　　安装成功！当然如果，现在可以使用`git –version`命令查看版本是否正确。如果发现无此命令，则有可能是环境变量没有设置正确：

 ```shell
 echo "export PATH=$PATH:/usr/local/git/bin" >> /etc/bashrc
 source /etc/bashrc
 ```

　　现在已经可以用 `git` 命令了，用 `git` 把 Git 项目仓库克隆到本地，以便日后随时更新：

```shell
# git clone git://git.kernel.org/pub/scm/git/git.git
```

#### 三、安装fish

　　对于CentOS来说，当然是不能直接yum安装的啦，不过还是有已经编译好的二进制rpm包可以使用。为了以后更新方便，还是加入repo后使用yum来安装和更新更方便，[官网](https://fishshell.com/)其实有每个发行版的详细安装方式，可自行前往：

　　对于 CentOS 7，请以根用户 root 运行下面命令：

```shell
# cd /etc/yum.repos.d/
# wget https://download.opensuse.org/repositories/shells:fish:release:2/CentOS_7/shells:fish:release:2.repo
# yum install fish
```

#### 四、安装oh-my-fish

　　这个就简单了，因为官网有[中文件docs](https://github.com/oh-my-fish/oh-my-fish/tree/master/docs/zh-CN)呀：

```shell
# curl -L github.com/oh-my-fish/oh-my-fish/raw/master/bin/install | fish   //安装oh-my-fish
```

##### 快速入门

```shell
# omf update //更新自身和已安装的所有插件和默认主题。
# omf install [<name> //安装插件
# omf list  // 显示本地已安装的插件列表。
```

##### 关于主题

这里有  [oh-my-fish库里的主题列表](https://github.com/oh-my-fish/oh-my-fish/blob/master/docs/Themes.md#fishface)  ，因为我不喜欢太过于花哨，所以这里我最喜欢的是  [simple-ass-prompt](https://github.com/oh-my-fish/oh-my-fish/blob/master/docs/Themes.md#simple-ass-prompt)

```shell
# omf theme <theme>
```

##### 升级oh-my-fish

**升级前务必备份dotfiles 和其他自定义的数据**

```shell
# curl -L github.com/oh-my-fish/oh-my-fish/raw/master/bin/install | sh
```

现在你可以安全的移除 `$fish_path`.

```shell
# rm -rf "$fish_path"
```

##### 默认shell

添加 Fish 到 `/etc/shells`:

```shell
# echo "/usr/bin/fish" | sudo tee -a /etc/shells
```

切换并保存默认 shell:

```shell
# chsh -s /usr/bin/fish
```

切换之前的 shell:

> 一般来说是 `/bin/bash`、`/bin/tcsh` 或者 `/bin/zsh`.

```shell
# chsh -s /bin/bash
```