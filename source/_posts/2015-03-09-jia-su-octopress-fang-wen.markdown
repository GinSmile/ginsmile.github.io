---
layout: post
title: "加速octpress访问"
date: 2015-03-09 14:30
comments: true
categories: Octopress
---

因为GFW的关系，我的博客访问速度超级慢。经常卡在`正在连接ajax.googleapis.com`或者`正在连接www.gravter.com`上，所以要在源码中删除这些代码。
以下为踢出某些访问不了的网站的方法：

*	在/source/_includes/head.html中找到所有带`googleapis`的代码注释掉
*	在/source/_includes/custom/head.html中找到所有带`googleapis`的代码注释掉
*	在/source/_includes/header.html中把class为profilepic的div删除掉。

