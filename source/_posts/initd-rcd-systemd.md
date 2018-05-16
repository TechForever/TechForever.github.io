title: init.d + rc.d VS systemd Ubuntu服务进程管理与开机自启动
date: 2018-04-05 18:20:26
permalink: initd-rcd-systemd
category: 技术
tags:
- Linux
- Ubuntu
- 服务进程管理
---

![](http://blogqn.qixincha.com/Systemctl_tw.png)

作为一名Ubuntu服务器深度用户，在Ubuntu 16.04版本之前一直使用`init.d` + `rc.d`套装来管理各种服务。

如：Nginx, ssh, docker, supervisord等。

Ubuntu 16.04 新增了一个服务进程管理工具 `systemd`。我将其理解为一个wrapper程序，封装了底层技术细节，使得交互更人性化。

两种命令都是无需安装任何包就可以直接使用，但是更推荐使用新的命令，以获得更佳的体验。

系统自带的各种服务，与dpkg包的默认配置的服务进程管理工具，都已迁移到systemd。足见官方对其推荐程度。

那么，`systemd`和`init.d` + `rc.d`分别是如何管理服务的呢？systemd的优势又在哪里？

<!-- more -->

咋们按先后顺序介绍，先讲`init.d`和`rc.d`，再演进到`systemd`， 便对上面的问题有客观的认识了。

剩下的就自己独立思考与Google吧🤪

## init.d - Ubuntu 14.04及更早的版本中服务进程管理的正确姿势
`service nginx reload` 想必大多数人都使用过类似指令。他就是用于执行各种`init.d`脚本的交互程序。

在讲init.d之前，先讲一下`/sbin/init`这个程序。该程序是Linux内核加载后启动的第一个进程，pid是1。后续的启动工作，都是由`/sbin/init`脚本直接或间接调用其他程序完成的。

* init.d中的各种守护进程的管理脚本，会在以下两种情况下被使用：

	1. 当用户使用`service`指令时。`service`的第一个参数是所支持服务，这些都在`/etc/init.d/`中以一个shell脚本的形态存在。而第二个参数指令， 则是对应脚本中的一个函数。只有指定服务的init.d脚本中，存在对应的函数时，`service`指令才能执行成功。上图就很容易明白了：
![](http://blogqn.qixincha.com/48039570-955D-4C1D-BA92-5B07E39D1787.png)
nginx 与 ssh 都在`/etc/init.d`目录下有对应的脚本，所以都能作为`service`的第一个参数调用。但是configtest函数仅在`/etc/init.d/nginx`中有定义，所以仅`service nginx configtest`能调用成功。而`service ssh configtest`则调用失败。
而`httpd`因在`/etc/init.d`下没有对应的脚本，便无法使用`service`对其进行管理。

	2. 开机启动时作为被开机执行等级定义程序调用，既后面讲到的rc.d，其工作是依赖`/etc/init.d`脚本的

* Tips:
	* 当使用`service`能启动服务，但停止或查看status时提示找不到服务。多半是服务配置文件中修改了pid文件路径。 而`/etc/init.d/script`脚本依赖pid来管理服务进程状态，而且脚本中的pid路径是写死的。一旦`/etc/init.d/script`中使用的pid路径与实际pid文件路径不符时，就无法做后续的所有管理工作了。一旦启动， 就脱离service控制了。
    * `/etc/init.d/`下的脚本需要有可执行权限 `chmod +x`。
	* 大多`init.d`脚本会依赖与`/etc/default/`目录中的配置文件，遇到问题时，不妨也排查一下这个目录中的配置文件是否正确。

## rc.d - 开机自启动与关机自停止注册信息管理
最开始学Linux时（大概是2010年），想要实现一些服务的开机自启动。在百度查到各种文章，大部分是教我如何在`/etc/bash.rc` 甚至 `~/.bashrc` 中添加执行代码来启动这个服务。这些方法不仅丑陋，而且让人难以理解。

理论上，Linux系统再启动时，会按顺序加载诸多脚本， 你把启动代码插到任何一个开机会被加载的脚本中，都可以实现开机自启动。但是只需稍花些心思，看一下sshd的启动方式，或用apt-get装一个nginx, 看看nginx的启动方式，便会发现这样做是不对的。

如果你就是往`bash.rc`里面加启动指令来实现开机启动的，那你多半也不知道如何取消nginx等服务的开机自启动。

即使在最新的Ubuntu操作系统中， `rc.d`依然是系统启动的必要工具。即使使用systemd的enable, disable来管理服务的开机自启动，他背后也会和systemd有一定的联动。

在较早的Ubuntu版本中，添加开机自启动的最佳实践就是使用rc.d工具。下面简单介绍几个常用指令：

1. 添加supervisord的开机启动: `update-rc.d supervisord defaults`
2. 查看supervisord在rc.d中的注册情况： `ls -l /etc/rc*.d | grep supervisord`
3. 从rc.d中移除supervosrd的注册： `update-rc.d -f supervisord remove`

**以上指令都依赖相应的init.d脚本**

以上指令可用于管理`rc.d`中注册的服务，那么，rc.d又是如何工作的呢？为何注册在其中的服务就会开机自启动呢？

* 首先， 看看`/etc`目录下的`rc*.d`们
![](http://blogqn.qixincha.com/61290685-C7D9-4A45-8C5A-EE05C9B367C0.png)
我们看到有8个`rc*.d`目录 + 1个`rc.local`文件。
先说一下8个目录， 其对应着Ubuntu的8个runlevel。不同的操作系统，支持的允许模式略有不同。 但都会提供若干`rc*.d`目录，用于管理不同系统运行模式下需要启动/停止的服务。不同操作系统的runlevel可见参考资料中的`Linux Runlevel`链接。
* Ubuntu的runlevel：
	* 0: 关机模式，里面一般都是停止服务的脚本（K打头）。我理解是执行关机指令时，系统会先进入这个runlevel, 然后停掉这里定义的所有服务，再最终实现安全关机。直接断电估计不会掉到这个模式里。
	* 1: 单人模式，类似Windows的安全模式， 仅启动root用户的几个必要进程，除root外的其他文件系统都不会挂载
	* 2: 无网络的多人模式，网络相关服务都不会启动，网卡也不会自动ifconfig。至于多人模式，Unix/Linux都是多任务多用户操作系统，同一时间点是允许有多个用户在系统中开多个shell的。Windows桌面版这种单用户操作系统需要退出再切换。
	* 3: 带网络的多人模式，一般的命令行模式
	* 4: 无用户模式，用于特殊场景，可进入模式后新建用户
	* 5: GUI模式， 会启动图像界面
	* 6: 重启模式， 类似0(关机模式), 也都是停止服务的脚本（K打头）。
	* S: 单人模式，无网络和各种服务进程的单人模式，跟1号模式类似，限制略宽松一些。
* rc.local: 此文件也是一个简单的shell脚本， 初始化时是空的。有一些不方便做init.d脚本的简单程序， 可以放在这个脚本中开机启动。该脚本虽然名字叫rc.local， 但是和其他`rc*.d`目录不同，其并不属于开机等级定义。而是在各运行模式（除0,6两个模式外的其他模式）的`rc*.d`中均在最后一步调起`/etc/init.d/rc.local`的start函数， 再由start函数完成调用。而`/etc/init.d/rc.local`的stop函数是空的，所以即时注册了`K03rc.local`文件到某个运行模式下，也不会发生`/etc/rc.local`脚本的调用

* `rc*.d`中注册的信息：尝试列出`/etc/rc3.d`(一般命令行模式)目录中的所有文件：
![](http://blogqn.qixincha.com/D4861105-42BE-4A22-8140-A7C4D16ECC2A.png)
	发现除README外所有文件均软链接到`/etc/init.d/`目录中的脚本。这些软连接的文件名规则如下：
	* K打头（Kxxxx）：会调用对应脚本的stop函数，也就是停掉对应服务。可以把K看作Kill的首字母
	* S打头（Sxxxx）：会调用对应脚本的start函数，也就是启动对应服务。可以把S看作Start的首字母
	* K/S后面的数字(K01xxxx/S02xxxx)：用于确定调用顺序，数字越小，越先调用。
	* 后面的字母没有多余的含义，只是使用`update-rc.d`脚本管理这些注册信息时，默认会使用服务名称

* 开机时作业顺序如下： 内核加载完毕 -> `/sbin/init`进程启动 -> 挨个调起对应运行模式下的`rc.d`中注册的服务的对应函数 -> 若注册了`S0xrc.local`信息会调起`/etc/rc.local`脚本
* 而关机/重启作业顺序如下： `/sbin/init`进程收到关机/重启信号 -> 挨个调起对应运行模式下的`rc.d`中注册的服务的对应函数（关机模式也可以注册S信息，只要业务需求） -> 最终关闭内核

## systemd - 更人性化的服务进程管理工具
* systemd主要改进是在人机交互与注册方式上，底层的作用原理上没有太多变化。我更倾向于把他理解为`init.d`脚本的一个wrapper程序,当使用systemd时注册与调用方式如下：

	1. systemd配置文件地址，类似init.d的/etc/init.d/目录：

			/lib/systemd/system/
    2. 执行以下命令前，需确保 `/etc/init.d/` 或 `/lib/systemd/system/` 下有对应服务的管理脚本。
    相关脚本一般都可google到，比如：https://github.com/zokeber/supervisor-systemd/blob/master/etc/systemd/system/supervisord.service
    也可以自己参考着现有脚本写
	2. 开机自启动，也会对应的写到rc.d中：

			systemctl enable nginx
	3. 查看所有的开机自启动：

			systemctl list-unit-files | grep enabled
	4. 移除开机自启动， 也会对应的写到rc.d中：

			systemctl disable nginx
	5. 管理服务：

			systemctl reload nginx
			systemctl restart nginx
			systemctl stop nginx
		
* 我们发现使用systemd后， 无需再关注底层的`init.d` 与 `rc.d`的工作原理与相互关系。该程序封装了底层技术细节，使得交互更人性化。


## 参考资料
* [Systemd 入门教程：命令篇 - 阮一峰](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
* [Systemd 入门教程：实战篇 - 阮一峰](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)
* [维基百科：Linux Runlevel](https://en.wikipedia.org/wiki/Runlevel)

