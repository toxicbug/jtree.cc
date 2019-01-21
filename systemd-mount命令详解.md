+++
title = "systemd-mount命令详解"
date = "2019-01-17"
tags = ["systemd-mount", "mount", "automount", "autofs"]
categories = ["Linux-Server", "Linux-base"]
+++

`systemd-mount` 用于创建并启动一个临时` .mount `或` .automount` 单元，也就是将` WHAT `文件系统挂载到 `WHERE` 目录。

`systemd-mount` 在功能上与传统的` mount` 命令很相似， 不同之处在于，`systemd-mount` 并不立即直接执行挂载操作，而是将挂载操作封装到一个临时的 `.mount `或` .automount `单元中，以接受 systemd 对各种单元的统一管理， 从而可以实现将挂载操作自动按照依赖关系排入任务队列、自动处理依赖关系、挂载前进行文件系统检查、按需自动挂载等好处。

在此之前，使用自动挂载的话，一般使用`autofs`来完成。但是`autofs`问题还是蛮多，现在有了`systemd-mount`之后，完全可以不用考虑`autofs`了。

我此前有一篇文章专业介绍了`.mount`文件和`.automount`文件，现在`systemd-mount`可以自动帮我们完成`.mount`和`.automount`的封装工作。

~~~shell
#语法
# systemd-mount [OPTIONS...] WHAT [WHERE]
# systemd-mount [OPTIONS...] --list
# systemd-mount [OPTIONS...] --umount WHAT|WHERE...
~~~

选项(OPTIONS):

~~~shell
--no-block
不同步等待临时挂载点单元完成启动操作。如果未使用此选项，那么 systemd-mount 将会在临时挂载点单元完成启动操作之后才返回。使用此选项之后， systemd-mount 将会立即返回，并以异步方式检查临时挂载点单元是否完成了启动操作。

--no-pager
不将程序的输出内容管道(pipe)给分页程序。

--no-ask-password
在执行特权操作时不向用户索要密码。

--quiet, -q
安静模式，也就是不显示额外的信息。

--discover
强制探测挂载源，也就是探测挂载源的文件系统卷标(label)及其他元数据，以帮助更有效的创建临时挂载点单元。 例如，将文件系统卷标以及设备型号组合在一起，作为临时挂载点单元的描述字符串。 又例如，如果检测到的块设备是U盘之类的可移动块设备，那么将会创建临时 automount 单元(而不是常规的 mount 单元)， 并且会自动为下文的 --timeout-idle-sec= 选项设置一个较小的值， 以确保及时卸载可移动文件系统，从而有效保证移动存储设备上的文件系统一致性。 如果仅设置了一个参数，那么此选项将被默认开启。

--type=, -t
指定要挂载的文件系统类型(例如 "vfat", "ext4", …)。 如果省略(或设为 "auto")则表示自动检测。

--options=, -o
设置明确的挂载选项。

--fsck=
控制是否在挂载之前先对文件系统进行一次检查，接受一个布尔值，默认值为 yes 。 对于 automount 单元(参见下文的 --automount= 选项)来说， 因为仅在首次访问块设备时才进行文件系统检查，所以可能会轻微的降低首次访问时的响应速度。

--description=
为临时 mount 或 automount 单元设置一个描述性的字符串。参见 systemd.unit(5) 的 Description= 选项。

--property=, -p
为临时 mount 单元设置一个属性。此选项接受的值的格式与 systemctl(1) 的 set-property 命令相同。

--automount=
控制是否创建一个临时 automount 挂载点。接受一个布尔值。 设为 yes 表示创建一个临时 automount 挂载点，也就是仅在首次实际访问该挂载点时才真正挂载实际的文件系统。 设为 no 表示创建一个临时 mount 挂载点，也就是立即真正挂载实际的文件系统。 自动挂载点的好处是按需自动挂载，并且可以使用下文的 --timeout-idle-sec= 选项设置一个空闲时间以实现自动卸载， 也就是，如果自动挂载点空闲(无访问)超过了指定的时长，那么它将被自动卸载。

如果明确或者隐含(仅设置了一个参数)的设置了 --discover 选项， 并且检测到的块设备是U盘之类的可移动块设备， 那么此选项的默认值是 yes (以减少意外拔出可移动块设备可能造成的文件系统不一致)， 否则默认值是 no 。

-A   ★★★★★
等价于 --automount=yes

--timeout-idle-sec=
设置 automount 单元的空闲超时时长。 也就是，如果自动挂载点空闲(无访问)超过了指定的时长，那么它将被自动卸载。 systemd.time(7) 手册详细的描述了时长的表示语法。 此选项对仅创建了临时 mount 单元的挂载点没有意义。

注意，如果明确或者隐含(仅设置了一个参数)的设置了 --discover 选项， 并且检测到的块设备是U盘之类的可移动块设备，那么此选项的默认值是"1s"(一秒)， 否则，默认值是 "infinity"(永不超时)。

--automount-property=
与 --property= 选项类似，不过仅作用于临时 automount 单元。

--bind-device=
控制是否将 automount 单元与对应的块设备存在期绑定，此选项接受一个布尔值，且仅对 automount 挂载点有效。 设为 yes 表示：当对应的块设备消失时，automount 挂载点将会被自动删除。 设为 no 表示：即使对应的块设备消失，automount 挂载点也依然被保留， 同时对该 automount 挂载点的访问将会被一直阻塞到重新插上对应的块设备。 此选项对非设备类文件系统(例如网络文件系统或虚拟内核文件系统)的挂载无效。

注意，如果明确或者隐含(仅设置了一个参数)的设置了 --discover 选项， 并且检测到的块设备是U盘之类的可移动块设备，那么此选项的默认值是 yes ， 否则，默认值是 no 。

--list     ★★★
命令列出所有本地已知可挂载的块设备的简明信息(包括例如文件系统卷标之类的元数据)。

-u, --umount     ★★★★★
停止挂载点(WHERE)或块设备(WHAT)对应的 mount 与 automount 单元。 使用此选项等价于直接使用 systemd-umount 命令，并且可以一次接受多个参数， 这些参数可以是挂载点、块设备、/etc/fstab 风格的设备节点、 包含文件系统的 loop 文件，例如 systemd-mount --umount /path/to/umount /dev/sda1 UUID=xxxxxx-xxxx LABEL=xxxxx /path/to/disk.img 。 注意，如果使用了 -H 或 -M 选项，那么挂载点必须只能用绝对路径表示。

--user
与当前调用用户的用户服务管理器(systemd 用户实例)通信， 而不是默认的系统服务管理器(systemd 系统实例)。

--system
与系统服务管理器(systemd 系统实例)通信， 这是默认值。

-H, --host=
操作指定的远程主机。可以仅指定一个主机名(hostname)， 也可以使用 "username@hostname" 格式。 hostname 后面还可以加上容器名(以冒号分隔)， 也就是形如 "hostname:container" 的格式， 以表示直接连接到指定主机的指定容器内。 操作将通过SSH协议进行，以确保安全。 可以通过 machinectl -H HOST 命令列出远程主机上的所有容器名称。

-M, --machine=
在本地容器内执行操作。 必须明确指定容器的名称。

-h, --help
显示简短的帮助信息并退出。

--version
显示简短的版本信息并退出。
~~~

