---
layout: post
title: "Octopress博客配置"
date: 2013-08-28 23:16
comments: true
categories: Octopress
---

以下介绍了我的Octopress博客的配置情况。   

0.添加主题
--------------------------------------------
Octopress有很多精美的主题，在这里可以看到很多：http://opthemes.com/ 。本博客使用了greyshade主题，该主题的作者是[Shashank Mehta](http://shashankmehta.in/)，主题的介绍页面在http://shashankmehta.in/archive/2012/greyshade.html。   
这里介绍了greyshade主题的安装：       
假设你已经安装好了默认主题，并且当前在octopress目录下，只需要输入以下命令即可：
    $ git clone git@github.com:shashankmehta/greyshade.git .themes/greyshade
    $ echo "\$greyshade: color;" >> sass/custom/_colors.scss 
    $ rake "install[greyshade]"
    $ rake generate
<!--more-->
   
1.添加About Me页面
------------------------------------
首先创建about页面：`rake new_page["about"]`，就会在创建source/about目录的同时创建index.markdown,使用编辑器编辑即可。    
然后打开source/_includes/custom/navigation.html，修改成如下：
    <ul class="main-navigation">
      <li><a href="{{ root_url }}/">Blog</a></li>
      <li><a href="{{ root_url }}/blog/archives">Archives</a></li>
      <li><a href="{{ root_url }}/about">About Me</a></li>
    </ul>
{% img right /images/about_shot.png 247 130 'image' 'images' %}
然后使用`rake generate`命令，就会产生相应的静态页面。使用`rake preview`预览即可。    
如果想在username.github.io中看到，不要忘了`rake deploy`以及git命令提交到github。   

<!--more-->
2.添加社会化分享按钮
-----------------------------------------
只需要添加著名的社会化分享按钮addthis.com即可。去[addthis.com](https://www.addthis.com/get/sharing#.Uh4X56EW3nM)注册,获取代码。    
将代码粘贴到source/_includes/post/sharing.html中。例如我的sharing.html文件为：
    <div class="sharing">
        <!-- AddThis Button BEGIN -->
        <div class="addthis_toolbox addthis_default_style addthis_32x32_style">
            <a class="addthis_button_sinaweibo"></a>
            <a class="addthis_button_twitter"></a>
            <a class="addthis_button_facebook"></a>
            <a class="addthis_button_pinterest_share"></a>
            <a class="addthis_button_google_plusone_share"></a>
            <a class="addthis_button_compact"></a><a class="addthis_counter addthis_bubble_style"></a>
        </div>
        <script type="text/javascript">var addthis_config = {"data_track_addressbar":true};</script>
        <script type="text/javascript" src="//s7.addthis.com/js/300/addthis_widget.js#pubid=ra-521dd13b3fa550d0"></script>
        <!-- AddThis Button END -->
   
3.添加社会化评论
-----------------------------------------
Octopress已经提供了一个评论插件，名为Disqus，我们只需要到Disqus注册帐号，在注册的时候输入网站域名（username.github.io即可）。注册时的shortname很有用，下面代码中要修改的就是shortname！！注册成功后修改octopress目录下的_config.yml文件：
    #Disqus Comments
    disqus_short_name: shortname
    disqus_show_comment_count: true
*另外，这里注意，在修改_config.yml的时候，一定要注意冒号后面有个空格，否则在`rake generate`的时候会有解析错误*    
最重要的一点，_config.yml文件修改好之后必须generate之后才能在本地preview到。而其他文件，比如post了一个新的文章，如果处在preview状态，刷新一下localhost:4000会立马看到。




4.添加新浪微博支持
-------------------------------------------
原始的greyshade主题不支持新浪微博，这里稍加修改，即可在页面左下角显示新浪微博的按钮。本修改方法来自于[allenhsu](http://www.imallen.com/blog/2013/05/12/add-support-for-weibo-and-dribbble-to-greyshade.html)，在这里向作者表示感谢。     
{% img left /images/social_shot.png 282 114 'image' 'images' %}     

首先，在sass/parts/_header.scss文件中去掉第316行,`margin-right: 15px;`更改为：
    margin-right: 5px;
    margin-bottom: 15px;
然后，还是在上一个文件里，在320行往后添加weibo相关代码，最终代码段如下：
            &:hover{
    				opacity: 1;
    			}
    			&.weibo{
    				background: image-url('social/weibo.png') center no-repeat #e32529;
    				border: 1px solid #e32529;
    				&:hover{
    				  border: 1px solid darken(#e32529, 10%);
    				}
    			      }			      
    			&.facebook{
    				background: image-url('social/facebook.png') center no-repeat #3B5998;
    				border: 1px solid #3B5998;
    				&:hover{
    					border: 1px solid darken(#3B5998, 10%);
    				}
    			}
第三步，在source/_includes/header.html中，修改代码，在类为social的div下加上如下代码：
    <div class="social">
    		{% if site.weibo_user %}
    		<a class="weibo" href="http://www.weibo.com/{{ site.weibo_user }}" title="Weibo">Weibo</a>
    		{% endif %}
最后，不要忘了在source/images/social文件夹下添加weibo.png图片，可以直接到我的github[下载](https://github.com/GinSmile/ginsmile.github.io/tree/03f65a6acf8b00988c294b01c701abfbfedb41a1/source/images/social/weibo.png)。    
如果还不清楚，可在我的github中查看修改过程。点击这个链接：[commit 添加博文：博客配置，添加新浪添加微博支持](https://github.com/GinSmile/ginsmile.github.io/commit/03f65a6acf8b00988c294b01c701abfbfedb41a1),由于我没有把以波浪结尾的临时文件（*~）添加到gitignore，这里有点乱，读者大可忽略所有以波浪线为结尾的文件改动。

   
5.imagepop plugin
-------------------------------------

这是一个添加图片的插件，可以按照比例缩放图片，点击图片可以查看原图，项目地址在[imgpop](https://bitbucket.org/fudanchii/imgpop/src/2fc043b1713e5de401edb0eea8639502bcc250a8?at=default)  
这里重复一下安装过程：   
> 在`octopress/Gemfile`中添加以下代码，就可以得到相应依赖：
        gem 'erubis'
        gem 'mini_magick'
> 运行`bundle install`,安装相应依赖
> 将开源项目中的`_style.scss`中的内容添加到`octopress/sass/custom/_styles.scss`中
> 将开源项目中plugins中的两个文件复制`octopress/plugins`文件夹下
> 将开源项目中的imgpop.js文件复制`octopress`文件夹下     
ok~~
   
参考资料：   
--------------------------------------
> [greyshade](https://github.com/shashankmehta/greyshade)   
> [yanjiuyanjiu.com](http://www.yanjiuyanjiu.com/blog/20130402/)    
> [zonyitoo.github.io](http://zonyitoo.github.io/blog/2012/04/14/octopresszhu-ti-ji.markdown/)   
> [imgpop](https://bitbucket.org/fudanchii/imgpop/src/2fc043b1713e5de401edb0eea8639502bcc250a8?at=default)  
