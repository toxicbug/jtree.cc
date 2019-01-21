+++
title = "sudo命令出现unable to resolve host 解决方法"
date = "2018-03-16"
tags = [ "sudo", "ubuntu", "base" ]
categories = ["Linux-Base", "Linux-Software"]
+++

Ubuntu/debian环境, 假设这台机器名字叫abc(机器的hostname), 每次执行sudo命令就出现这个警告讯息:

```shell
sudo: unable to resolve host abc
```
虽然sudo 还是可以正常执行, 但是警告讯息每次都出来非常恼人,而这只是机器在反解上的问题, 所以就直接从/etc/hosts 设定, 让abc(hostname) 可以解回127.0.0.1 的IP 即可.其实非常简单，只要改成下面两行即可：

```	shell
127.0.0.1       localhost 
127.0.0.1       abc    #要保证这个abc与 /etc/hostname中的主机名一致才有效
```

这样设完后, 使用sudo 就不会再有那个提示信息了。