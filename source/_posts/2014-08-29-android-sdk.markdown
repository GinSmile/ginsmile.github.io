---
layout: post
title: "android-sdk"
date: 2014-08-29 23:25
comments: true
categories: Android 
---

记下我重新安装android开发环境的过程，一句话，万恶的长城啊！

这次是第二次还是第三次装这个开发环境啊，我不太记得了，不过每次遇到的问题都差不多，故记录下来，以备下次用到。这次和前几次稍微不一样，使用了Intellij IDEA，看网上说这玩意比Eclipse好很多，就试了试，目前还不是很习惯的说。



下面是安装过程： 

- 首先要安装Intellij IDEA
- 然后确保本机安装有JDK
- 去官网安装android sdk（那个alone的版本就好）
- 打开sdk的`sdk/tools/android`命令，出现Android SDK Manager
- 用此SDK Manager下载各个版本的API

这次问题主要出现在最后一步，总是出现fetch不上的错误。原因就是dl-ssl.google.com 被干掉了。我们可以用[站长工具](http://ping.chinaz.com)的超级ping工具来查找可用的IP。找到之后，修改`etc/hosts`文件的内容即可。即在此文件的文件尾加上：

    203.208.46.146 dl.google.com
	203.208.46.146 dl-ssl.google.com
	127.0.0.1 servserv.generals.ea.com


<!--more-->