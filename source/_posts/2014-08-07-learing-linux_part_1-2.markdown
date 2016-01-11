---
layout: post
title: "learing linux,part 1,2"
date: 2014-08-07 22:53
comments: true
categories: Linux
---

此文为学习《鸟哥的私房菜》时候的备忘录.

- 第一部分 Linux的规则与安装
- 第二部分 Linux文件、目录与磁盘格式
<!--more-->


##第一部分 Linux的规则与安装
---
###CPU频率相关
- CPU的外频：CPU与外部组件进行数据传输、运算时的速度，如333MHz
- CPU的频率：CPU每秒钟可以进行的工作次数，如3.0GHz
- CPU的倍频：CPU内部用来加速工作性能的一个倍数，外频 * 倍频 ＝ 频率，如9倍，333M * 9 = 3.0G

CPU每次能够处理的数据量称为字组大小（work size），字组大小为32bit的称为32bitCPU


---
###显示内存的计算
屏幕像素个数 * 每像素占内存大小 ＝ 显示屏幕内容所需要的内存      
*（1024 * 768） * 3B ＝ 2.25MB*，注：全彩的像素每个颜色占一个字节，红黄蓝。

---
###硬盘相关
512Bytes的扇区，扇区围成一个圆就成为磁道，所有盘面的同一个磁道组成一个柱面。     
分区的最小单位是柱面，磁盘存储的最小单位是扇区。

---
###Linux是什么
Linux是一套操作系统，包括内核和系统调用两层。    
硬件--内核--系统调用--应用程序

---
###分区
- 硬盘可以分为主分区（三个或四个）和扩展分区（最多一个），扩展分区可以继续切出逻辑分区来。
- 主分区和扩展分区最多只有四个
- 只有逻辑分区和主分区能够格式化

**第一个可开机设备的第一个扇区共512bytes，其中MBR（masster boot recorder，主引导区）占446bytes，分区表（partition table）占64bytes，分区标志2bytes。每个分区记录占16bytes，所以最多能分四个分区。**


---
###开机流程
- BIOS，开机主动执行BIOS，认识第一个可开机的设备
- MBR，第一个可开机设备的第一个扇区内的主引导分区块，包含引导加载程序
- 引导加载程序，读取内核文件
- 内核文件，开始操作系统的功能





##第二部分 Linux文件、目录与磁盘格式

---
###文件权限
Linux中的文件有很多的属性，用来保护数据的安全性。

	xujindeMacBook-Pro:text xujin$ ls -l
	total 992
	-rw-r--r--@ 1 xujin  staff  507711  7 26 08:36 pic.jpg
【权限】-【连接】-【所有者】-【用户组】-【文件大小】-【修改日期】

- chgrp，改用户组，chgrp othergroup a.jpg
- chown，改所有者，chown otheruser a.jpg
- chmod，改权限
	- chmod 764 a.jpg
	- chmod rwxrw-r-- a.jpg
	- chmod u=rwx, g=rw-, o=r-- a.jpg
	
**一个文件是否能够被执行，仅仅与上面命令中显示的权限有关，与扩展名无关**

---
###特殊目录
>
- [.]   当前目录
- [..]  上一级目录
- [-]   前一个工作目录
- [~]   目前用户所在的主文件夹
- [~account] 用户account所在的主文件夹


---
###文件目录管理的基本命令
- ls,cd,pwd,mkdir,rmdir
- cp,rm,mv
- cat,nl,more,less,head,tail,od
- touch,file
- which,whereis,locate,find

---
###文件系统
Linux最主要的文件系统为Ext2，该文件系统包括：superblock，inode，block。


