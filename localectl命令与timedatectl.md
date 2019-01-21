+++
title = "localectl命令与timedatectl命令"
date = "2018-03-04"
tags = [ "localectl", "timedatectl", "base" ]
categories = ["Linux-Base", "Linux-Software"]
+++

　　对于国外的VPS来说,基本都不会默认安装中文支持的,但是我们有些中文目录或者中文文件就比较麻烦,而且几乎所有国外VPS都使用UTC时间,与国内常用的CTS相差8个时间,所以这里们修改一下让时间和国内一至,同时支持显示中文文和目录

<!-- more -->

#### localectl命令

　　我们先用`localectl`或者`localectl status`来查询当前系统所使用的locale:
　　
```shell
$ localectl
   System Locale: LANG=C
       VC Keymap: us
      X11 Layout: us
```

　　一般来说终端要能正确显示中文,只需要使用UTF-8即可,所以我们先在`/etc/locale.gen`文件中,把`en_US.UTF-8 UTF-8`前面的#去掉.然后使用`locale-gen`命令,让系统处理这些locale.
　　最后一步,就是更改系统使用的locale:

```
# localectl set-locale LANG=en_US.utf8
```

#### timedatectl命令

　　还是老办法,先使用`timedatectl status`命令查看一下当前时间状态:

```
$ timedatectl
      Local time: 日 2018-03-04 02:59:30 UTC
  Universal time: 日 2018-03-04 02:59:30 UTC
        RTC time: 日 2018-03-04 02:59:29
       Time zone: UTC (UTC, +0000)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
```

然后我们看一下命令的基本用法

```
$ timedatectl
list-timezones            -- Show known timezones
set-local-rtc             -- Control whether RTC is in local time
set-ntp                   -- Control whether NTP is enabled
set-time                  -- Set system time
set-timezone              -- Set system timezone
status                    -- Show current time settings
```

好吧,先`list-timezones`查看有多少时区,哇太多了,还是加个搜索范围吧:

```
$ timedatectl list-timezones | grep Shanghai
Asia/Shanghai
```

出来了,上海.然后设置时区`set-timezone Asia/Shanghai`即可,当然也可以`set-timezone UTC`,不再多说.要注意我们还有一个修改时区的工具`tzselect`.按下图一步步选择即可.
![](http://p.jtree.cc/Snip20180304_4.png)

goodluck!





