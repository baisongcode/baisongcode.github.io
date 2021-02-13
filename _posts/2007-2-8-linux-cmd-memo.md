---
layout: post
title:  Linux命令备忘
date:   2007-2-8
categories: 读书看报
---
Linux那些命令老是记了忘忘了记，干脆写一个备忘，以便随时来查找。

一、命令

（一）系统信息

1） uname -a 查看系统信息。

2） cat /proc/version 同上。

3） cat /etc/redhat-release 查看Redhat Linux的版本

4） free -m 以MB为单位查看内存

5)  rm myfile >rm.log 2>&1  输出命令的系统错误信息
0、1和2分别表示标准输入、标准输出和标准错误信息输出，可以用来指定需要重定向的标准输入或输出

6)  uptime：查看系统已经运行了多长的时间

7)  w：查看谁登录及他们在干什么

（二）磁盘管理

1）du --max-depth=1 -h 查看当前目录下各子目录的大小

2）fdisk -l  查看各分区的大小及格式

3）/sbin/hdparm -i /dev/hda  查看硬盘的硬件属性

4）/sbin/hdparm -t /dev/hda  测试硬盘读性能

5）/sbin/hdparm -cd /dev/hda 显示当前IO和DMA设置

6）/sbin/hdparm -c1 /dev/hda 打开32位I/O

7）/sbin/hdparm -d1 /dev/hda 打开DMA（现在一般都默认打开）

8）/sbin/hdparm -X66 /dev/hda 设置为ATA33（X68-->ata66, X69-->ata100）
这些命令只在本次开机中生效，而下一次开机又要重新设置。如果要实现每一次开机时都生效，应该在文件/etc/rc.d/rc.local 的结尾加入以下命令：hdparm -c 1 -d 1 -k 1 /dev/hda，它可以使设置在每次重新启动系统时生效。

（三）文件管理

1） find /usr -name "*svn*" -print 查找文件
详见《Linux文件查找命令find,xargs详述》http://www.linuxsir.org/main/?q=node/137

2） rm -fr aaa 删除目录aaa

3） chgrp -R baisong msp3 把文件夹msp3及其子目录的所属组改成baisong

4） chown -R baisong msp3 把文件夹msp3及其子目录的所有者改成baisong

（四）vi相关

1）/xxx  向下查找xxx

2）?xxx  向上查找xxx

3）n  查找下个关键字

（五）RPM相关

1）rpm -aq 查看当前系统中安装的所有rpm包

2）rpm -ivh *.rpm  安装某软件包

3）rpm -e [package name] 删除某rpm包

（六）其他

1） PATH=$PATH:/usr/local/maven
export PATH 加一个环境变量

2） setup 配置xwindows，键盘，时区，防火墙，启动的服务等。

3） tar -zxvf xxx.tar.gz 解压缩
tar -jxvf xxx.tar.bz2

1)  编译内核时，注意是否装了ncurses-devel包。

二、配置文件

1） /etc/sysconfig/i18n 默认语言

2） /boot/grub/grub.conf grub配置文件

3） 以字符界面开机：
找到/etc/inittab这个文件，然后打开，找到这么一行:
id:5:initdefault.
把5改成3保存,重新启动