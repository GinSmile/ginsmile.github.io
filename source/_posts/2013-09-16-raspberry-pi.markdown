---
layout: post
title: "Raspberry Pi 把玩记录"
date: 2013-09-16 20:16
comments: true
categories: RaspberryPi
---

树莓派
=============================
树莓派是个好东西！

###1. 配置
它是一款基于Linux的卡片大小的微型电脑，我买的是红板（国产B版）： 

    树莓派主板
    散热片
    开关电源线
    网线 
    class10 8G SD卡

由于我是在笔记本上利用远程登陆控制树莓派，所以以下配件不需要，等回家有电视了之后再鼓捣：
    无线网卡
    HDMI线 

<!--more-->
{% img right /images/pic/Pi_Logo.png 190 240 'Pi_Logo' 'Pi_Logo' %}

###2. 安装过程中遇到的问题

在安装中文的时候，有一个错：
    
    perl: warning: Setting locale failed
这时候，在home目录中，打开.bashrc `vi .bashrc`，在.bashrc文件內底部添加下面一句即可：


    
    export LC_ALL=C

###3. 使用windows的mstsc登陆树莓派（远程桌面方式）
在putty中输入以下命令，安装xrdp：

    sudo apt-get install xrdp

然后再windows的运行中输入：mstsc进入远程桌面，输入树莓派的IP地址即可


###4. 在树莓派上安装weibo sdk
首先安装pip

    sudo apt-get install python-pip
然后

    pip install sinaweibopy
    
###5. 利用samba树莓派和PC共享文件夹
由于Raspberry Pi内存很小，在上面编程非常卡，所以我就利用windows和树莓派的共享文件夹，在windows下编写程序，然后复制到tmp文件夹里。   
利用下面命令安装samba

    sudo apt-get install samba
    sudo apt-get install samba-common-bin
配置，修改`/ect/samba/`目录下
    + 重命名smb.conf为smb.conf.backup
    + 新建一个文件，命名为smb.conf,内容如下：
    
           [global]
           log file = /var/log/samba/log.%m
           [tmp]
           comment = Temporary file space
           path = /tmp
           read only = no
           public = yes
    + 保存完毕后输入`sudo /etc/init.d/samba retsart`，开启samba服务。
    + 在树莓派终端输入`sudo useradd admin`创建用户admin
    + 在`/etc/samba/`文件夹下建立smbpasswd文件，命令为：`sudo touch /etc/samba/smbpasswd`
    + 设置密码，命令`sudo smbpasswd -a admin `
    + 此时，就能在windows的网络里面找到名为RASPBERRY的网络，然后点进去输入刚才的用户名，密码，这里的tmp文件夹即为共享文件夹。   

Tips：   
我利用这个文件夹和Github实现项目的云同步的功能，首先在windows上编写程序，上传到Github上，在网络tmp文件夹下`git clone`和`git pull`来更新。这样即使树莓派不安装git也可以。
   

###参考资料
[爱板网](http://www.eeboard.com/bbs/thread-5473-1-1.html)   

[树莓派论坛](http://www.shumeipai.net)

    
