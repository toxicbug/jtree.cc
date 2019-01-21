+++
title = "Mac与Win双系统时间不同步的解决办法"
date = "2016-12-07"
tags = ["Mac", "Base" ]
categories = ["Mac"]
+++

　　很多刚使用mac的用户一般都会选择安装双系统来解决对mac系统陌生的问题，不过有些用户发现在安装了windows系统和OS X系统后系统的时间有时候会出现不一致的情况，有时候windows的时间会比OS X系统的时间要慢非常多。
为什么Mac的时间和Windows的时间不一样？
因为Mac默认BIOS时间为格林尼治标准时间，而Windows默认BIOS时间为本地时间，我们所在的中国以北京（地理上的东八区，+8:00 GMT），所以如果不进行设置，Mac和Win的时间会相差8小时。
解决方法其实很简单，只需如下两步即可:

步骤：

1. win系统里时间同步服务器改为`time.asia.apple.com`

2. 注册表`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\`中添加一项数据类型为`REG_DWORD`,名称为`RealTimeIsUniversal`,值设为`1`.

或者直接运行命令:

```bash
管理员打开CMD运行":
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1  
```