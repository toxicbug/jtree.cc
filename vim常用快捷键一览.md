+++
title = "vim常用快捷键一览"
date = "2018-02-08"
tags = [ "vim", "快捷键", "base" ]
categories = ["Linux-Base", "Linux-Software"]
+++

vim 是对vi的扩展，文中的很多操作是vi通用的.
vi是**区分大小写**的命令的，也就是说 `g`与`G` 是不同的命令.
在不同模式下，快捷键是不一样的 模式分 `一般模式` `编辑模式` `指令模式`
一般模式用于文件内部操作，编辑模式用于输入编写等，指令模式用于对文本文件进行操作.
本文主要收集了一般模式中用于快速定位光标和复制粘贴等操作的快捷键.

<!-- more -->

###### 1.翻页
`Ctrl+b`  上翻页    ;     `Ctrl+f`  下翻页       ;     `Ctrl+e`  下滚动

###### 2.光标移动

移动到文档的首尾:            `gg`    文档第一行.     ;      `G`   文档末行
移动到屏幕的首尾:            `H`      屏幕最上   ;     `M`    屏幕中央     ;   `L`    屏幕最下
以行为单位移动:                `n+`    下移n行    ;     `n-`    上移n行     ;    `nG`    下移到n行首    ;    `n$`    上移到n行尾
行内移动:                           `0`      行首        ;      `$`       行尾
以单词为单位:                   `e`      后移一词至词尾   ;  `w`    后移动一词至词首     ;   `b`    前移一词至词首

###### 3.复制、粘贴、剪切、删除

`nyy`    复制n行    ;    `ndd`    剪切n行
`d0`     剪切本行光标前的内容    ;    `d$`     剪切本行光标后的内容    ;    `dG`     剪切光标后所有字符(含其它行)
`p`       粘贴

###### 4.撤销、更换大小写

`u`        撤销上一次操作           ;          `~`        更换当前光标位置的大小写

###### 5.查找

`/code`    向后查找code字符       ;     `?code`    向前查找code字符
`n`    查找下一处     ;       `N`    查找上一处

