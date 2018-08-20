+++
title = "VPS上部署Hexo博客环境"
date = "2017-01-17"
tags = ["hexo", "Blog" ]
categories = [
	"Linux-blog",
   "Linux-server",
	"Linux-Software"
]
+++

我之前写过一个关于MacOS环境部署Hexo的文章,后来我在实际使用过程发现还是有很多不方便,比如我的mac在家,那么在办公室或者手机上要发博客是基本不可能的事情,我为此也很苦恼,但是有一天我突发奇想,把Hexo博客环境搭建在我的VPS上,不管在windows下还是手机上,都可以用Markdown写好上传到VPS即,然后在SSH终端一个`hexo d -g`命令即可发布.
那么我们现在开始部署Hexo环境:

<!-- more -->

### 一、安装git
这个简单,一条命令即可:
```
CentOS:
yum install git
Ubuntu:
apt-get install git
```
### 二、安装node.js
#### 1.CentOS下安装node.js

```
curl -sL https://rpm.nodesource.com/setup | bash -
然后:
yum install -y nodejs
```
#### 2.Ubuntu下安装node.js

```
curl -sL https://deb.nodesource.com/setup | bash - 
然后: 
apt-get install -y nodejs  
```

### 三、部署Hexo环境
因为安装包在墙外，如果国内VPS可以考虑用`淘宝源安装(http://npm.taobao.org/)`,如果VPS本身就在国外或者有代理的话:
#### 1.直接安装hexo:
```
npm install -g hexo-cli --no-optional
```
#### 2.部署目录
进入要部署的目录,执行部署命令:
```
cd ~/Document/hexo
hexo init
npm install
```
至此,hexo环境已经部署完成.
### 四 安装NexT主题
在线安装命令如下:
```
cd your-hexo-site
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
### 五 具体使用
具体使用就和之前一样了,这里只重点提一下几个常用命令:
#### 1.安装git部署插件
```
npm install hexo-deployer-git --save

npm install hexo-generator-cname --save
```
#### 2.添加文章
```
hexo new "postName"
```
#### 3.添加页面
```
hexo new page categories
hexo new page tags
配置:
title: 标签
date: 2014-12-22 12:39:04
type: "tags"
comments: false

title: 分类
date: 2014-12-22 12:39:04
type: "categories"
comments: false

```
#### 4.生成并上传
```
hexo d -g
```
#### 4.deploy设置
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

像我使用的是新浪的SAE，https://git.sinacloud.com/xxxxx ；
可以使用

```
https://abc@qq.com:123456@git.sinacloud.com/xxxxx 
```

的形式，但是两个@就会让hexo心寒。。。

解决办法是用“%40”替代“@”，%40是@的url编码。

### 六 使用小技巧
#### 1.文本居中引用

例如:
```
{% cq %}
拥有思想的瞬间，是幸福的；
拥有感受的快意，是幸福的；
拥有父爱也是幸福的。

琼 瑶
{% endcq %}

```
#### 2.分割线
```
<!-- more -->
```