+++
title = "在Mac下通过HEXO搭建博客"
date = "2016-10-21"
tags = ["hexo", "Mac", "blog" ]
categories = ["Mac", "Linux-Blog"]
+++

　　经过一番折腾，总算是把Hexo给弄好了。在这期间遇到了各种问题，网上有的教程也有点老了，这里就再写一篇。最新的教程可以去[Hexo官网](https://hexo.io/zh-cn/docs/index.html)查看。

## 前期准备

因为mac自带Git,但是需要Xcode才能运行,这个直接从App Store上下载就好了，也可以在控制台执行git命令,系统会提示安装Xcode,大约5分钟左右.没什么难度。

<!-- more -->

### 安装node.js

Hexo是基于node.js的，所以要去[官网](https://nodejs.org/)上下载下来安装。版本可以选择稳定版(4.3.1)也可以选择最新版(5.7.0)。需要注意的是，Hexo 3.1.1测试的最低版本为0.12，所以安装的版本不要太旧，之前看到网上装的0.8.4的版本，我也这么装，结果有一大堆的报错。

### 注册Github账户

在本地搭建好Hexo后可以将内容同步到github上，可以在网上浏览。可以去[Github官网](https://github.com/)上去注册，注册的过程我就不罗嗦了，具体的过程可以去这个页面上跳到Github的那部分去看。

其中配置SSH Keys的那部分，可以选择不配制，不配置的话以后每次提交的时候就需要手动输入账号密码，如果配置了的话就不需要了。

## 正式安装

因为安装包中有些内容在墙外，所以可以换[淘宝源](http://npm.taobao.org/)(好用)，或者用

```
npm install -g hexo-cli --no-optional
```

来安装,然后进入你要安装的目录，如:

```
cd ~/Document/hexo
```

然后安装

```
hexo init
```

安装好之后不要忘记执行

```
npm install
```

至此，就已经安装完毕了。是不是很简单呢？

当然还要安装主题目。我喜欢NexT主题，在线安装如下：

```
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

## 后期部署

### 添加文章

```
hexo new "postName"
```
其中postName是博客名。

### 生成静态页面

```
hexo generate
```

或者也可以执行缩写

```
hexo g
```

### 本地启动

执行好上面的命令之后就可以在本地启用服务来看效果了。执行下面的命令：

```
hexo sever
```

或缩写

```
hexo s
```

看到 

```
INFO Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop. 
```

之后，就可以在浏览器中打开页面

```
http://localhost:4000
```

来看了。

## 上传至Github

### 安装git部署插件

在部署之前，首先我们要确认在你的Github帐号的Repository中有 

```
user.github.io 
```

的项目。在确认之后，就可以执行命令来安装插件:

```
npm install hexo-deployer-git --save
```


## 配置 _config.yml 文件

在Hexo安装的目录，如 ~/Document/hexo 中找到 _config.yml 文件。打开。翻到最后，找到 deploy 字样，改成如下格式：

```
deploy: 
  type: git 
  repo: https://github.com/user/user.github.io.git 
  branch: master
```

需要注意的是：冒号后面有一个空格；使用github可以不用写branch那一行。
如果要使用多个 deployer，可改成如下样式：

```
deploy:
-  type: git
   repository: ssh://git@github.com/user/xxxx.git
   branch: gh-pages
-  type: git
   repository: ssh://git@git.coding.net/user/xxxx.git
   branch: home-pages
```

## 同步
输入命令

```
hexo deploy
```

或者缩写

```
hexo d
```

来执行。以后每次执行就可以依次输入下面三行命令：

```
hexo clean
hexo generate
hexo deploy
```

或者其缩写。

## 最后优化
### 插件
我使用了几个常见的插件：

#### 从Wordpress迁移到Hexo

```
npm install hexo-migrator-wordpress --save
```

在 WordPress 仪表盘中导出数据(“工具(Tools)” → “发布(Export)” → “文章(WordPress)”)插件安装完成后，执行下列命令来迁移所有文章。source 可以是 WordPress 导出的文件路径或网址。

```
hexo migrate wordpress <source>
```

#### 站点地图

```
npm install hexo-generator-sitemap --save
```

生成的sitemap.xml可以给搜索引擎收录使用。
如果要生成百度的sitemap，使用以下命令：

```
npm install hexo-generator-baidu-sitemap --save
```

#### RSS订阅

```
npm install hexo-generator-feed@1.0.3 --save
```

配置文件里经常看见的/atom.xml就是由这个插件生成的

### 主题
官方给了很多的主题提供参考，默认的是landscape。
我使用的是[NexT(官网)](http://theme-next.iissnan.com)。
## 小技巧
### 文本居中的引用
此标签将生成一个带上下分割线的引用,并且引用内文本将自动居中.多行文本若长度不等,视觉上会显得不对称,因此建议在引用单行文本的场景下使用.例如作为文章开篇引用或者结束的总线引用.
使用方式
* HTML方式:使用这种方式时,给`img`添加属性`class=blockquote-center`即可.
* 标签方式:使用`centerqute`或简写`cq`.
> 此标签要求NexT版本在`0.4.5`以上.
例如:`标签调用法`

```
<!-- HTML方式:直接在Markdown文件中编写HTML来调用 -->
<!-- 其中class="blockquote-center"是必须的 -->
  <blockquote class="bloclkquote-center">blah blah blah</blockquote>
```