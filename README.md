这是一个Hexo博客。

共有两个分支：

*	master是静态网站代码。
*	hexo是源代码。作为网站的备份。




<hr>
本项目产生的过程。

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


<hr>
其中，为了更新第三方主题[next](https://github.com/iissnan/hexo-theme-next)方便，让第三方的next主题作为一个git子项目添加到本项目中。		
新增next主题的时候使用的命令如下：

	cd Blog
	git submodule init themes/next
	git submodule update
