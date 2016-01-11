---
layout: post
title: "如何用 Android Studio 导入开源项目以及常见错误的解决办法"
date: 2015-05-23 16:54
comments: true
categories: Android
---

本篇以Github上最热门的MaterialDesign库，[MaterialDesignLibrary](https://github.com/navasmdc/MaterialDesignLibrary)来介绍怎样使用Android Sudio导入开源项目的，如果你和我一样刚刚从Eclipse转到AS，那本篇文章非常适合你。

如果不引入任何第三方库，不做自动化分渠道打包等操作，那可以在完全不了解Gradle的情况下进行Android项目的开发。但如果要想导入Github上的热门项目，必须首先熟悉Gradle。
###1. Gradle
> Gradle是以Groovy为基础，面向java应用，基于DSL语法的自动化构建工具。是google引入，替换ant和maven的新工具，其依赖兼容maven和ivy。

简单来说，Gradle就是Andriod Studio引入的一种自动构建工程的工具，很好的解决了Android项目的构建。大概能解决以下开发过程中的痛点：

* **不用把第三方库下载下来**。以前用Eclipse必须得把第三方库下载下来，然后在自己的项目中引入那个第三方library才行。但使用Gradle，几行代码就搞定第三方库的依赖，降低了耦合性。
* **不用手动修改N次代码，把代码打N个包发到N个市场**。以前可能需要针对不同的渠道发不同的APK文件包，现在只需要在gradle中的几行代码就能一次生成多个渠道包。具体可参考此文章：[如何通过Gradle实现一套代码开发不同特性的APK](http://ghui.me/blog/20150310/create-several-variants-of-an-app-in-gradle/)。

<!--more-->


如果你用AS新建一个项目，那此项目的默认项目结构如图所示，图中打开的文件是TestMe/build.gradle，已经给出了每句话的含义。

{% img center /images/pic/gradle_test.png 'gradle' 'gradle' %}

图中新建项目名为TestMe，使用了Project的目录结构。此目录下可见以下几个gradle文件，分别是：

* TestMe/build.gradle，全局gradle，在这个文件里的设置会在整个项目里通用。
* TestMe/app/build.gradle，模块的gradle，仅在app模块通用。
* TestMe/settings.gradle，用于指定哪个目录是模块model。里面的内容默认是`include ':app'`，表示app这个文件夹是一个模块。

而TestMe/app/build.gradle文件内容如图所示，具体解释参见图中注释。

{% img center /images/pic/gradle_test2.png 'gradle' 'gradle' %}


###2. 导入MaterialDesignLibrary项目
我们的目标是把MaterialDesignLibrary项目在AS里面成功运行，并在手机上运行成功。

####2.1 第一步
首先，用git的clone命令将[MaterialDesignLibrary](https://github.com/navasmdc/MaterialDesignLibrary)下载到本地。

####2.2 第二步
进入AS，File->Import Project，然后找到MaterialDesignLibrary目录，双击该目录下的build.gradle即可导入项目。

####2.3 第三步
{% img right /images/pic/gradle_mdl.png 300 'gradle' 'gradle' %}

可以看导入好的Github上的MaterialDesignLibrary项目包含两个module，包括MaterialDesignDemo和一个MaterialDesign。

* MaterialDesign的build.gradle文件第一行为：`apply plugin: 'com.android.library'`，表示这是一个Library。
* MaterialDesignDemo的build.gradle文件第一行为：`apply plugin: 'com.android.application'`，表示这是一个应用。

他的这个项目使用的是引用本地已有的MaterialDesign库，而本文第三部分要讲的是引用网上中心库的MaterialDesign库。两种方法都可以，但后一种更加方便更常用，第一种方法不太常用。
第二步之后，可能会出现很多错，通常是因为配置问题，这部分详见本文下面的常见错误。     
注意：一定要确保根目录下的setting.gradle里面是`include ':MaterialDesign', ':MaterialDesignDemo'`。表示以上两个文件夹都是module。

###3. 在自己的项目中引用MaterialDesignLibrary
还是之前的TestMe项目，只需要在TestMe/app/build.gradle中的dependencies中加入`compile 'com.github.navasmdc:MaterialDesign:1.5@aar'`即可，如下：

```
dependencies {
    // 编译libs目录下的所有jar包
    compile fileTree(dir: 'libs', include: ['*.jar']) //多个文件
    //compile files('libs/foo.jar')   //单个文件这样写
    compile 'com.android.support:appcompat-v7:22.0.0' //编译第三方库
    compile 'com.github.navasmdc:MaterialDesign:1.5@aar'
}
```


###4. 常见错误
AS在导入开源的项目或者第三方库的时候经常会出现的错误如下。其实很多都是各种工具的版本问题。

####4.1
> Error:failed to find Build Tools revision 21.1.1    
<a href="install.build.tools">Install Build Tools 21.1.1 and sync project</a>

是因为该开源库对应的Build Tools的版本你的AS里面没有。解决方法是点击他的链接进行下载。或者更改每个model里面的build.gradle文件中的`buildToolsVersion "21.1.1"`为本机里面有的Build Tolls版本。

####4.2
> Error:failed to find target android-18 : /Users/xujin/develop/adt-bundle-mac-x86_64-20140702/sdk    
> 
<a href="install.android.platform">Install missing platform(s) and sync project</a>

是因为该开源库对应的android sdk的版本你的机器里面没有。解决方法同上，要么下载，要么改改配置。更改每个model里面的build.gradle文件中的` compileSdkVersion 21`为本机里面有的sdk版本。

####4.3
> Error:(44, 30) 错误: -source 1.6 中不支持 diamond 运算符 (请使用 -source 7 或更高版本以启用 diamond 运算符)

出错的原因是因为代码中有 `List<Car>  cars = new ArrayList<>();`，java在1.7引入了钻石运算符`<>`,解决办法有两种。一是更改编译的java sdk版本，而是将出错代码改为`List<Car>  cars = new ArrayList<Car>();`


####4.4
> Error:(37) No resource identifier found for attribute 'checked' in package 'com.gc.materialdesign'

是因为第37行的`materialdesign:checked="true"`，命名空间materialdesign里面没有checked属性，改成check即可，代码如下。（之前遇到过，姑且说说。）

```
<com.gc.materialdesign.views.CheckBox
                android:id="@+id/checkBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerInParent="true"
                android:background="#1E88E5"
                materialdesign:check="true"
                 />
```



###参考资料
* [importing-libraries-into-android-studio](http://www.crowbarsolutions.com/importing-libraries-into-android-studio/)
* [Android Studio系列教程四--Gradle基础](http://stormzhang.com/devtools/2014/12/18/android-studio-tutorial4/)
* [MaterialDesignLibrary](https://github.com/navasmdc/MaterialDesignLibrary)