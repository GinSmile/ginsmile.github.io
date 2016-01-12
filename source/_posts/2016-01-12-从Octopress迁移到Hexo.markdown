---
title: 从Octopress迁移到Hexo
date: 2016-01-12 18:40:16
categories: Hexo
---

由于最近Hexo非常流行，我也把我的博客改成Hexo。生命在于折腾啊～

1.安装Hexo
---
参照[Hexo官方文档](https://hexo.io/docs/index.html)，安装git，npm，Node.js。然后`$ npm install -g hexo-cli`安装好Hexo。


2.初始化Hexo
---

	$ hexo init <folder>
	$ cd <folder>
	$ npm install
<!--more-->
3.修改设置以适应Octopress文件名
---
修改 _config.yml：

	new_post_name: :year-:month-:day-:title.markdown
	
4.其他基本设置的修改
----
根据[官方文档](https://hexo.io/docs/configuration.html)，修改 _config.yml文件内容。

5.使用next主题
----
这一部分可以根据[next文档](http://theme-next.iissnan.com/)来设置。

6.使用github备份网站代码
----
原因：由于原始的Hexo只有一个master分支，不利于保存原始代码。所以本项目的目的是创建一个项目，有两个分支，master分支是静态网站，hexo分支是原始代码。

* 在本地搞定Hexo博客之后。运行`hexo s`后，在localhost:4000可以看到网站内容。
* 在主站的配置文件_config.yml末尾，设置如下，代表每次用`hexo d`命令deploy的时候是更新到master分支。
	*	repository: git@github.com:GinSmile/ginsmile.github.io.git
  	*	branch: master
* 在GitHub上新建`ginsmile.github.io`。
* 运行`Hexo d`命令将网站部署到GitHub上。此时，GitHub上就有了一个仅有master分支的项目。
* 在本地git clone git@github.com:GinSmile/ginsmile.github.io.git Blog
* 创建hexo分支
	* cd Bolg
	* git checkout --orphan hexo   #**创建一个分支，这个分支是独立的**
	* git checkout hexo
	* git rm -rf .  #**删除原来代码树下的所有文件**
	* rm '.gitignore'
* 把源代码拷贝到这个分支
* 修改.gitignore，过滤掉一些每次更新都自动生成的文件
	* vim .gitignore	

	>
		_Store
		Thumbs.db
		db.json
		*.log
		node_modules/
		public/
		.deploy*/

* git push origin hexo


以后，每次丢失资料后，都需要进行以下步骤

1. git clone git@github.com:GinSmile/ginsmile.github.io.git Blog
2. cd Blog
3. npm install hexo
4. npm install
5. npm install hexo-deployer-git

