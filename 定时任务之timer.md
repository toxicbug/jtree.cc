+++
title = "定时任务之timer"
date = "2019-02-20"
tags = [ "systemd", "timer", "service" ]
categories = ["Linux-Base", "Linux-Software"]
+++

#### 一、简介

crond和atd服务基于分钟的，意思是说它们每分钟醒来一次检查是否有任务需要执行。如果有任务的执行需要精确到秒，crond和atd是无能为力的。在基于systemd的系统上，可以通过计时器systemd.timer来实现精确到秒的计划任务。

systemd要实现定时任务需要用到两个`unit`:`.service`和`.timer`.其中`.service`负责配置需要运行的任务，`.timer`负责配置执行时间。

#### 二、配置服务

###### 1、编写好要运行的脚本文件

我这里需要定时删除一些临时文件，所以我制作了一个名为`dell-tb.sh`脚本,放到`/usr/local/bin`目录中：

```bash
#!/bin/bash
find /mnt -name '~~~@*'|xargs -i rm -rf {} ;
find /mnt -name '*.dwl'|xargs -i rm -rf {} ;
find /mnt -name '*.bak'|xargs -i rm -rf {} ;
find /mnt -name '*.err'|xargs -i rm -rf {} ;
find /mnt -name '*.lsp'|xargs -i rm -rf {} ;
find /mnt -name '*.lsp_bak'|xargs -i rm -rf {} ;
find /mnt -name '*.fas'|xargs -i rm -rf {} ;
find /mnt -name '*.lnk'|xargs -i rm -rf {} ;
find /mnt -name '*.dwl2'|xargs -i rm -rf {} ;
find /mnt/dates/nextcloud.date/ -name "plot.log"|xargs -i rm -rf {} ;
find /mnt/dates/nextcloud.date/ -name "debug.log"|xargs -i rm -rf {} ;
sudo -u www php /www/wwwroot/www.****.com/nextcloud/occ files:scan --all
```

###### 2、配置好`.service`服务

```shell
$ sudo systemctl cat mydell.service
# /etc/systemd/system/mydell.service
[Unit]
Description=MyDellScript

[Service]
Type=simple
ExecStart=/usr/local/bin/dell-tb.sh
```

###### 3、配置计时器`.timer`

```shell
$ sudo systemctl cat mydell.timer
# /etc/systemd/system/mydell.timer
[Unit]
Description=Runs MyDellScript every hour

[Timer]
OnBootSec=5min
OnUnitActiveSec=60min
Unit=mydell.service
Persistent=true

[Install]
WantedBy=basic.target
```

#### 三、启动、状态查询、停用

```shell
# systemctl enable mydell.timer
Created symlink from /etc/systemd/system/basic.target.wants/mydell.timer to /etc/systemd/system/mydell.timer.

# systemctl start mydell.timer

# systemctl status mydell.timer
● mydell.timer - Runs MyDellScript every hour
   Loaded: loaded (/etc/systemd/system/mydell.timer; enabled; vendor preset: disabled)
   Active: active (running) since 三 2019-02-20 10:16:37 CST; 5s ago

2月 20 10:16:37 jkhxc systemd[1]: Started Runs MyDellScript every day.

# systemctl status mydell
● mydell.service - MyDellScript
   Loaded: loaded (/etc/systemd/system/mydell.service; static; vendor preset: disabled)
   Active: inactive (dead) since 三 2019-02-20 10:17:05 CST; 30min ago
  Process: 26020 ExecStart=/usr/local/bin/dell-tb.sh (code=exited, status=0/SUCCESS)
 Main PID: 26020 (code=exited, status=0/SUCCESS)

2月 20 10:17:04 jkhxc dell-tb.sh[26020]: Starting scan for user 14 out of 1...)
2月 20 10:17:04 jkhxc dell-tb.sh[26020]: Starting scan for user 15 out of 1...)
2月 20 10:17:04 jkhxc dell-tb.sh[26020]: Starting scan for user 16 out of 1...)
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: Starting scan for user 17 out of 1...)
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: Starting scan for user 18 out of 1...)
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: +---------+-------+--------------+
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: | Folders | Files | Elapsed time |
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: +---------+-------+--------------+
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: | 5854    | 31354 | 00:00:21     |
2月 20 10:17:05 jkhxc dell-tb.sh[26020]: +---------+-------+--------------+
Hint: Some lines were ellipsized, use -l to show in full.

# systemctl stop mydell.timer
```

#### 四、配置详解

###### 1、周期执行

`systemd`的服务单元配置文件中被不同的`标签`分隔成不同的配置区块，其中：

`[Unit]` 标签下指定了不依赖于特定类型的通用配置信息，比如例子中两个文件都指定了一个选项`Description=`表示描述信息。

`[Install]` 标签下保存了本单元的安装信息，其中`WantedBy=`表示当使用`systemctl enable`命令启用该单元时，会在指定的目标的`.wants/`或`.requires/`下创建对应的符号链接(如上例)。这么做的结果是：当指定的目标启动时本单元也会被启动。

除了这两个所有配置文件都可以设置的标签外(其余选项可以通过命令`man 5 systemd.unit`查看)，每个服务单元还有一个特定单元类型的标签，比如我们例子中`.service`文件中的`[Service]`和`.timer`文件中的`[Timer]`。

`[Service]` 标签下`Type=`后的值指明了执行方式，设置为`simple`并配合`ExecStart=`表明指定的程序(我们例子中的脚本)将不会`fork()`而启动；如果设置为`oneshot`表明只执行一次(类似`at`)，如果需要让systemd在服务进程退出之后仍然认为该服务处于激活状态，则还需要设置`RemainAfterExit=yes`。其余选项请用命令`man 5 systemd.service`查看

`[Timer]`标签中可以指定多种单调定时器，所谓"单调时间"的意思是从开机那一刻(零点)起， 只要系统正在运行，该时间就不断的单调均匀递增(但在系统休眠时此时间保持不变)，永远不会往后退，并且与时区也没有关系。 即使在系统运行的过程中，用户向前/向后修改系统时间，也不会对"单调时间"产生任何影响。包括：

```bash
OnActiveSec=       表示相对于本单元被启用的时间点
OnBootSec=         表示相对于机器被启动的时间点
OnStartupSec=      表示相对于systemd被首次启动的时间点
OnUnitActiveSec=   表示相对于匹配单元(本标签下Unit=指定的单元)最后一次被启动的时间点
OnUnitInactiveSec= 表示相对于匹配单元(本标签下Unit=指定的单元)最后一次被停止的时间点
```

我们的例子中使用了其中的两个`OnBootSec=5min`和`OnUnitActiveSec=60min`指定**本机**在启用之后5分钟调用`Unit=`后的单元，并在此单元被启用后每隔60分钟再次启用它，达到了定时周期性的执行的目的。

这些定时器后指定的时间单位可以是：us(微秒), ms(毫秒), s(秒), m(分), h(时), d(天), w(周), month(月), y(年)。如果省略了单位，则表示使用默认单位`秒`。可以写成`5h 30min`表示之后的5小时30分钟。

###### 2、日历定时

`[Timer]`标签下还可以设置基于挂钟时间(wall clock)的日历定时器`OnCalendar=`，所谓"挂钟时间"是指真实世界中墙上挂钟的时间， 在操作系统中实际上就是系统时间，这个时间是操作系统在启动时从主板的时钟芯片中读取的。由于这个时间是可以手动修改的，所以这个时间既不一定是单调递增的、也不一定是均匀递增的。其时间格式可以是：

``` bash
Thu,Fri 2019-*-1,5 11:12:13  #表示2019年任意月份的1日和5日，如果是星期四或星期五，则在时间11:12:13执行
*-*-* *:*:00                 #表示每分钟
*-*-* 00:00:00               #表示每天
*-01,07-01 00:00:00          #表示每半年
*:0/15                       #表示每15分钟
12,14,13:20,10,30            #表示12/13/14点的10分、20分、30分
Mon,Fri *-01/2-01,03 *:30:45 #表示任意年份奇数月份的1日和3日，如果是周一或周五，则在每小时的30分45秒执行
```

单调定时器和日历定时器的其他内容可以通过命令`man 7 systemd.time`查询，[Cron表达式在线生成器](http://cron.qqe2.com/)

###### 3、其它

`Unit=`后指明了与此计时器相关联的服务单元(如本例中的mydell.service)。
服务单元中的大部分设置选项允许指定多次，不相冲突的情况下将均生效，如`.timer`中可以设置**多个Unit**表示这些服务单元共用一个计时器。

另外`[Timer]`标签下还可以设置选项`Persistent=`，它**只对**`OnCalendar=`指令定义的日历定时器有意义。如果设为`yes(true)`(默认值为`no(false)`)，则表示将匹配单元的上次触发时间永久保存在磁盘上。 这样，当定时器单元再次被启动时， 如果匹配单元本应该在定时器单元停止期间至少被启动一次， 那么将立即启动匹配单元。 这样就不会因为关机而错过必须执行的任务。(类似于`anacron`的功能)

#### 五、参考资料

[systemd.timer 中文手册](http://www.jinbuguo.com/systemd/systemd.timer.html#)

[linux基础命令介绍十四：定时任务](https://segmentfault.com/a/1190000007916299#articleHeader2)