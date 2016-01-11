---
layout: post
title: "重装系统后恢复Octopress博客"
date: 2014-01-04 22:03
comments: true
categories:  Octopress
---

之前在虚拟机安装的linux，运行一会就内存爆掉了，实在不好玩，这次实打实地安了一个双系统，真的很快，不过把Octopress恢复在一个全新的系统上真的很麻烦。
关键是之前安装的很多插件，恢复过来就用不了了。    

<!--more-->

1.首先要按照安装全新Octopress那样，弄出来一个能安装Octopress的环境,安好rvm,Ruby1.9.3之类的，详见[用GitHub Pages和Octopress搭建博客](http://ginsmile.github.io/blog/2013/08/28/yong-github-pageshe-octopressda-jian-bo-ke/#.UsgqCqx5MxA)。    
2.然后，要clone下来source分支：
	$ git clone -b source git@github.com:username/username.github.com.git octopress
3.进入octopress目录，clone下_deploy来：
	$ cd octopress
	$ git clone git@github.com:username/username.github.com.git _deploy 
4.安装bundler，使用bundler安装所需插件
	$ gem install bundler
	$ rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
	$ bundle install
5.设置github pages页面关联
	$ rake setup_github_pages


过程虽然这么简单，但是在这之后，我`rake generate`的时候，出现了不少错误，比如之前新添加的tag不能用的错误，此时只能删了重新设置一遍。


参考资料
----------
> [boboshone](http://boboshone.com/blog/2013/06/05/write-octopress-blog-on-multiple-machines/)   

