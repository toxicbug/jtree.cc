+++
title = "git浅入门"
date = "2018-08-21"
tags = [ "git", "ohmyzsh" ]
categories = [
	"Linux-Server",
    "Linux-base"
]
+++

首先当然是要安装git啰，`yaourt -Sy git`.对了这里也给出:[ohmyzsh内置git插件的别名wiki地址](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git) 以便查用。

然后就是设置用户名和邮箱：

```sh
$ git config --global user.name "My Name"
$ git config --global user.email myEmail@example.com
```

##### 一、本地仓库操作

 * 创建新仓库`git init`

 * 检查状态`git status`

 * 提交到缓存区`git add`

    * 可以提交目录下的所有内容`git add -A`
    * 也可以只提交单个文件`git add index.html`

* 提交到本地git库`git commit`

```sh 
$ git commit -m "Initial commit."
# -m “Initial commit.” 表示对这次提交的描述，建议使用有意义的描述性信息。
```

* 创建本地分支

  * 每一个仓库默认分支都叫master, 创建新分支可以这样：

  ```shell
  $ git branch amazing_new_feature
  # amazing_new_feature为新分支，它跟当前分支同一起点
  ```

* 切换分支

  * 单独使用git branch，可以查看分支状态：

  ```shell
  $ git branch
    amazing_new_feature
  * master
  ```

  * `*` 号表示当前活跃分支为master，使用git checkout切换分支。

  ```shell
  git checkout amazing_new_feature
  ```

* 合并分支`git merge`

##### 二、远端仓库操作

* 链接远端仓库

  * 为了能够上传到远端仓库，我们需要先建立起链接

  ```shell
  $ git remote add origin https://github.com/tutorialzine/awesome-project.git
  ```

  * 一个项目可以同时拥有好几个远端仓库为了能够区分，通常会起不同的名字。通常主远端仓库被称为origin。

* 上传到服务器

  * 每次我们要提交代码到服务器上时，都会使用到git push。
  * `git push`命令会有两个参数，远端仓库的名字，以及分支的名字：

  ```shell
  $ git push origin master
  
  Counting objects: 3, done.
  Writing objects: 100% (3/3), 212 bytes | 0 bytes/s, done.
  Total 3 (delta 0), reused 0 (delta 0)
  To https://github.com/tutorialzine/awesome-project.git
   * [new branch]      master -> master
  ```

  * 上传速度取决于你使用的服务器，push过程你可能需要验证身份。如果没有出差错，现在就可以使用浏览器去查看远端仓库的内容。

* 克隆仓库

  * 放在Github上的开源项目，人们可以看到你的代码。可以使用 git clone进行下载到本地。

  ```shell
  $ git clone https://github.com/tutorialzine/awesome-project.git
  ```

  * 克隆完成后，在本地也会创建一个新的仓库，并自动将github上的分支设为远端分支。

* 从服务器上拉取代码

  * 如果你更新了代码到仓库上，其他人可以通过git pull命令拉取你的变动：

  ```shell
  $ git pull origin master
  From https://github.com/tutorialzine/awesome-project
   * branch            master     -> FETCH_HEAD
  Already up-to-date.
  ```

  * 因为暂时没有其他人提交，所有没有任何变动.

