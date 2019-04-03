+++
title = "搭建Jupyter Notebook Server平台"
date = "2019-03-31"
tags = [ "python", "jupyter", "notebook", "anacondas3" ]
categories = ["Linux-Server", "Linux-blog", "Linux-software"]

+++

#### 一、为什么会选择Anaconda3

其实PythonIDE有很多，比如著名的`PyCharm`，但是`jupyter notebook`可以运行在服务器上，以网页的形式运行。而且自带非常多的函数库，就算自己安装也很简单，一条命令就搞定。其实``Anaconda3`的优点很点，这里就不再一一赘述了，大家自行百度。

#### 二、安装Anaconda3

##### 1.下载并安装

先在 [官网](https://www.anaconda.com/) 或者 [清华镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)下载对应平台的软件即可。本文下载的是Linux平台对应的`Anaconda3-2018.12-Linux-x86_64.sh`安装脚本。然后执行：

```bash
$ wget https://repo.anaconda.com/archive/Anaconda3-2018.12-Linux-x86_64.sh
$ sudo yum install bzip2
$ bash Anaconda3-2018.12-Linux-x86_64.sh
```

根据提示安装即可。但是有一点需要注意，这个脚本会因为系统中没有安装bzip2而中断。所以大家可以提前安装。此外根据需要确定是否安装vscode，因为我是安装在VPS里面的，主要使用vim进行编辑，所没没有必要安装它，而且安装时间非常长。

##### 2.配置环境变量

安装完成后，我们还需要对环境变更进行添加，方便启动。一般都可以通过修改`/etc/profile`或者`/etc/bashrc`来达到添加到环境变量的目的。因为我习惯使用非常root帐户，而且使用的是`oh my zsh`所以我只以修改`~/.zshrc`文件来作演示,在文件末尾添加以下内容，保存退出，最后执行`source ~/.zshrc`：

```bash
#Anaconda
export PATH=$PATH:/home/xxx/anaconda3/bin
```

##### 3.使用国内镜像

至于为什么就不多说了，国外VPS可以条过此节。使用详情或者安装其它源可参考 [清华Anaconda源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

```bash
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
$ conda config --set show_channel_urls yes
```

##### 4.升级conda和安装其它函数库

```bash
$ conda --version  #获取版本号
$ conda --help  #获取帮助
$ conda list  #列举当前所有包
$ conda upgrade --all  #更新所有包
$ conda install numpy  #安装一个包，这里安装的是numpy。
$ conda remove numpy  #删除一个包，这里删除的是numpy。
```

##### 5.启动jupyter notebook server

架设jupyter的坑还是蛮多的，反正我是一个人用，安装要求也不高，为了尽量减少折腾，我只修改了一个配置项，另外再增加了一个密码。具体过程如下：

###### 5.1 生成配置文件

```bash
$ jupyter notebook --generate-config
$ vi ~/.jupyter/jupyter_notebook_config.py
```

默认情况下`jupyter notebook server`只能本地运行：`127.0.0.1:8888`或者 `locahost:8888`。所以我们要修改配置：

将`将 #c.NotebookApp.ip = 'localhost'`项修改为`c.NotebookApp.ip = '*'`

测试一下，能否启动：

```bash
$ jupyter netobook
```

如果能正常启动，且外网可以访问 http://ip:8888 的话，即表示成功。

###### 5.2 设置密码

官方给出三种设置密码的方法，手动设置hashed password的方法，较为麻烦，也容易出错，这里只讲前两种：

第一种：进入网页后，在网页末尾进行设置。以下是官网原文：

>### Automatic Password setup
>
>As of notebook 5.3, the first time you log-in using a token, the notebook server should give you the opportunity to setup a password from the user interface.
>
>You will be presented with a form asking for the current _token_, as well as your _new_ _password_ ; enter both and click on `Login and setup new password`.
>
>Next time you need to log in you’ll be able to use the new password instead of the login token, otherwise follow the procedure to set a password from the command line.
>
>The ability to change the password at first login time may be disabled by integrations by setting the `--NotebookApp.allow_password_change=False`

第二种：在服务器用命令设置密码：

>### Automatic Password setup
>
> Starting at notebook version 5.0, you can enter and store a password for your notebook server with a single command. **jupyter notebook password** will prompt you for your password and record the hashed password in your `jupyter_notebook_config.json`.
>
> ```
> $ jupyter notebook password
> Enter password:  ****
> Verify password: ****
> [NotebookPasswordApp] Wrote hashed password to /Users/you/.jupyter/jupyter_notebook_config.json
> ```
>
> This can be used to reset a lost password; or if you believe your credentials have been leaked and desire to change your password. Changing your password will invalidate all logged-in sessions after a server restart.

#### 三、参考文档：

[《那些在线的jupyter](https://www.jianshu.com/p/e009997ab5d8)》 、  《[微软官方Azure Notebook文档](https://docs.microsoft.com/zh-cn/azure/notebooks/)》  、《[微软Azure Notebook试玩](https://www.jianshu.com/p/639a6c8532c9)》

