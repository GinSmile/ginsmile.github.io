---
layout: post
title: "Weibo Robot"
date: 2013-09-19 12:01
comments: true
categories: [python, RaspberryPi]
---


最近偷偷搞了一个微博机器人，第一版算是完成了吧，虽然还有bug，但不影响使用。    
这个机器人是基于新浪微博的python api，搭建在我的Raspberry Pi上的，因为我想让她更像一个人，所以加了很多图片和语录，导致程序有点大，仅仅图片就20M....OMG~~    
以下是她的功能：
{% img right /images/pic/sina_logo.png 224 71 'sina' 'sina' %}
>1. 每小时发布一条密友微博，以监控树莓派的状态
>2. 每天发布7条普通微博，[图片 or 问候语 or 监控信息] 随机搭配
>3. 回复提到我的原创微博，随机回复其[天气预报 or 问候语]
>4. 回复提到我的评论（不包括点击回复按钮那种评论），随机回复其一些评论
<!--more-->
下面是项目地址：https://github.com/GinSmile/QRobot   
   
Demo：http://weibo.com/u/3798238610


