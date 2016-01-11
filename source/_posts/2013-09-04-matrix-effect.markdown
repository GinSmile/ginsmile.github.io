---
layout: post
title: "matrix effect"
date: 2013-09-04 10:13
comments: true
categories: python
---

最近在学习python，其中的游戏库pygame也是刚刚入门。发现其实游戏编写并不是很难，逻辑清晰，一点点实现，一个稍大点的游戏完全可以做。由于刚刚入门，所以就写了一个小程序，用来实现类似黑客帝国的01弹幕效果。   
这个小程序主要利用了pygame的font库，首先生成一条长度随机速度随机的字幕，然后将其顺时针旋转90度，代码中注释非常详细，这里不再赘述。
<!--more-->

实现代码如下
{% gist 6426839 %}

效果图如下：
<img class="center" src="/images/program_shot/matrix.png" width="449" height="357" title="welcome" alt="welcome"> 
