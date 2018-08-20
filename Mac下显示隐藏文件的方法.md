+++
title = "Mac下显示隐藏文件的方法"
date = "2016-10-24"
tags = ["Mac", "base" ]
categories = [
	"Mac"
]
+++

对于新手而言，苹果的MAC操作系统刚用时用得很不习惯，比如想要显示被隐藏的文件时，不像windows有个“文件夹选项”对话框可以来设置，而且命令又长又不好记,所以我把它记录到我的博客里既方便自己也方便别人.

OS X 10.9 Mavericks以上的版本，显示和隐藏文件的命令如下：
```
defaults write com.apple.finder AppleShowAllFiles Yes && killall Finder //显示隐藏文件
defaults write com.apple.finder AppleShowAllFiles No && killall Finder //不显示隐藏文件
```
复制命令，在“终端”中粘贴命令，按下enter键--执行。