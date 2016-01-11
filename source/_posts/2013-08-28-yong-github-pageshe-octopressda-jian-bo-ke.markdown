---
layout: post
title: "用GitHub Pages和Octopress搭建博客"
date: 2013-08-28 19:42
comments: true
categories: Octopress
---
本文介绍了在ubuntu 12.04下使用GitHub Pages和Octopress搭建博客的过程。如果读者英文阅读能力良好，强烈建议读者去官方网站阅读文档，其文档已经包含了成功搭建一个博客的绝大部分内容。   
文档地址：[GitHub Pages](https://help.github.com/categories/20/articles)，[Octopress Document](http://octopress.org/docs/)   
注意：根据官方文档中的步骤安装Ruby 1.9.3可能会遇到一个错误,解决方法请看本文后面部分。
  
    
<!--more-->
搭建流程
==============

1.注册GitHub帐号
---------------------------------------
按照标准流程来注册帐号，创建名为username.github.io的repo，不要添加任何文件。


2.安装git
---------------------------------------
    $ sudo apt-add-repository ppa:git-core/ppa
    $ sudo apt-get update
    $ sudo apt-get install git
   
设置git，务必和github上相同
    $ git config --global user.name "your username"
    $ git config --global user.email "you email"   
    
3.产生SSH Key
------------------------------------------
首先查看目录中是否有SSH Key
    $ cd ～/.ssh
    $ ls
    # Lists the files in your .ssh directory
如果目录中没有SSH，则要生成一个新的，做法如下
    $ ssh-keygen -t rsa -C "your_email@example.com"
    ......
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/you/.ssh/id_rsa):
    # 这里直接打回车，按提示输入密码。（必须输入密码！）
    ........
    # 如果成功，则会显示：
    Your identification has been saved in /home/you/.ssh/id_rsa.
    ........
如果有SSH Key或者刚刚生成了新的SSH，则把SSH添加到剪贴板：
    $ sudo apt-get install xclip
    # Downloads and installs xclip.
    
    $ xclip -sel clip < ~/.ssh/id_rsa.pub
    # Copies the contents of the id_rsa.pub file to your clipboard

4.在github中设置SSH
-------------------------------------------------
去GitHub主页，进入Account Settings->SSH Keys->Add SSh Key，把剪贴板中剪贴板中的SSH key粘贴到里面，over。


5.使用RVM安装Ruby 1.9.3
----------------------------------------
这里是用RVM安装Ruby 1.9.3。   
首先在终端里输入ruby -version，查看是否安装过ruby。一般如果没有之前没有用过ruby，就不会显示已安装过1.9.3版本。这时候，和官方文档不同，首先要卸载系统默认的ruby和rvm。    
*这里详细请看网上的一篇博文：[Cloud's Blog](http://zuyunfei.com/2013/04/01/install-ruby-on-ubuntu/)*，这里复制下命令来备份，再次感谢原作者。   
卸载系统默认ruby以及rvm
    $ sudo apt-get remove --purge ruby-rvm ruby
    $ sudo rm -rf /usr/share/ruby-rvm /etc/rvmrc /etc/profile.d/rvm.sh
    $ rm -rf ~/.rvm* ~/.gem/ ~/.bundle
    
禁止安装doc
    $ echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc
    
安装必要的工具和库
    $ sudo apt-get install -y \
      git \
      build-essential \
      curl \
      wget
      
将rvm的配置加入.bashrc中
    $ sudo apt-get install -y \
      build-essential \
      openssl \
      libreadline6 \
      libreadline6-dev \
      curl \
      git-core \
      zlib1g \
      zlib1g-dev \
      libssl-dev \
      libyaml-dev \
      libxml2-dev \
      libxslt-dev \
      autoconf \
      libc6-dev \
      ncurses-dev \
      automake \
      libtool \
      bison \
      subversion \
      pkg-config \
      libgdbm-dev
安装sqlite3
    $ sudo apt-get install -y \ sqlite3 \ libsqlite3-dev
安装rvm和ruby，*期间可能会提示安装ruby1.9.3,根据提示安装即可*。
    $ curl -L https://get.rvm.io | bash -s stable --ruby
    $ source ~/.rvm/scripts/rvm
    $ rvm reload
    $ rvm use default
    $ ruby --version 
安装cyaml和bundler
    $ gem install cyaml --no-ri --no-rdoc
    $ gem install bundler --no-ri --no-rdoc

此时输入命令：`ruby --version`,会显示ruby 1.9.3，表示已经安装好了。

6.安装Octopress
--------------------------
克隆 Octopress项目
    $ git clone git://github.com/imathis/octopress.git octopress
    $ cd octopress
安装依赖
    $ gem install bundler
    $ rbenv rehash
    $ bundle install
安装默认主题
    $ rake install
 
7.部署网站
----------------------------------------
使用此命令`rake setup_github_pages`设置Octpress和Github的联系。
这条命令将：
> - 询问你的GitHub Pages仓库的URL地址
> - 重命名远端分支名从这‘origin’到站‘octopress’
> - 将GitHub Pages项目作为默认的远端分支
> - 将活动分支将从master转换为source
> - 根据你的Git仓库地址设置博客的URL
> - 在_deploy分支设置一个master分支，部署用，最终页面的目录最终即最终此文件夹
使用以下命令在本地预览
    $ rake generate  //此命令会产生最终的静态页面
    $ rake preview   //此命令会打开4000端口,可输入localhost:4000预览博客
使用以下命令将c产生你的blog，产生的文件到_deploy目录，然后自动commit，push到github的master分支。
    $ rake generate
    $ rake deploy    
将改动将更新到github
    $ git add .
    $ git commit -m 'your message'
    $ git push origin source
   
截止到此，一个基于GitHub Pages和Octopress的博客就搭建好了。 现在打开username.github.io就会看到刚刚搭建好的博客，注意：只有deploy到github上之后才会在username.github.io看到最终效果.
    
8.发布第一篇博文
-------------------------------------------------------
生成博文框架
    $ rake new_post[‘文章标题’]
此时可以在source/_posts文件夹下看到刚创建的网页。使用markdown语法写一篇博客即可（此处可使用retext编辑Markdown文件）。
    $ rake generate  //此命令会产生最终的静态页面
    $ rake preview   //此命令会打开4000端口,可输入在浏览器localhost:4000预览
    $ rake deploy    

9. 安装retext编辑Markdown文章
--------------------------------------------------------
网上有不少可以编辑Markdown文件的文档，这里推荐一款retext，适合liunx。
    $ sudo apt-add-repository ppa:mitya57
    $ sudo apt-get update
    $ sudo apt-get install retext

   
   
   
参考资料
----------------------------------  
[GitHub Help](https://help.github.com/articles/generating-ssh-keys)   
[GitHub Pages](https://help.github.com/categories/20/articles)   
[Octopress Document](http://octopress.org/docs/)    
[zuyunfei.com](http://zuyunfei.com/)
