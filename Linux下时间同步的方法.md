+++
title = "Linux下时间同步"
date = "2017-01-14"
tags = [ "ntpdate", "localtime", "base"  ]
categories = ["Linux-base", "Linux-software", "Linux-server"]
+++

​	众所周知，Linux服务器如果做同步的话，时间是相当重要的，所以我在这里给大家一个同步时间的方法：
## 操作如下：
与一个已知的时间服务器同步
```
ntpdate time.nist.gov
如果提示没有ntpdate，可以yum或者apt-get安装：
yum install ntpdate
```


然后删除本地时间并设置时区为上海：
```
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
如果提示：
```
ntpdate[1432]: Can't adjust the time of day: Operation not permitted
```
是因为OPENVZ虚拟机不允许修改时间，那么我们只执行如下代码即可：
```
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```


