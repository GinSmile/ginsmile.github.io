---
layout: post
title: "learing linux,part 3"
date: 2014-08-07 22:53
comments: true
categories: Linux
---
此文为学习《鸟哥的私房菜》时候的备忘录.

- 第三部分 学习shell与shell script
<!--more-->

##第三部分 学习shell与shell script
----
###vi的使用

* 一般模式，`vi a.c` 进入此模式。移动光标，删除字符，删除整行，复制粘贴
* 编辑模式，输入“i，o，a，r”任何一个字母进入。 
* 命令行模式，输入“[:],[/],[?]”中任一个字符进入。查找数据，读取保存，大量替换字符，离开，显示行号等。

一般过程：

    touch a.c
    vi a.c
    //get into vim
    ...
    //input 'i' to enter edit modle
    ...
    //edit the file
    ...
    //tap 'esc' to enter order modle
    //tap ':wq' to store the changes(or tap ':wq!' to compel to save the file )
    
vim仅需要熟记几种常用命令即可。

----
###bash基础
 通过shell将我们输入的命令与内核（kernel）通信，好让内核可以控制硬件来正确无误地工作。**诸如ls，cd都是独立的应用程序。通过shell来操作这些应用程序。**    
shell的版本

- sh（bourne SHell）
- C SHell(Sun里的)
- K SHell(商业常用)
- bash（Bourne Again SHell）

*可以用 cat /ect/shells 命令查看shells文件的内容，找到可用的shell*，而bash是Linux的默认shell
  
-----
###shell常用功能

- 命令记忆，按[上下箭头]选择历史纪录
- 文件补全，按[tab]
- 命令补全，按[tab]
- 别名，如`alias lm＝'ls -al'`
- shell script
- wildcard（通配符）

-----
###shell变量
1.环境变量，如

- @SHELL，默认shell的版本
- @PATH，执行文件查找的路径
- @HOME，用户的主文件夹    
- @HISTSIZE，history size，历史命令的条数
- @LANG，语系数据
- @RADOM，0～32767之间的随机整数

2.自定义变量，如:

    myvar＝one
    echo @myvar    //会显示one
- 取消变量使用`unset myvar`。 
- 使用`export`使自定义变量变成环境变量。    
   
3.可以利用变量来进行目录的简化：
    
    Jyoshin:/ xujin$ prac="/Users/xujin/practice/"
    Jyoshin:/ xujin$ cd $prac
    Jyoshin:practice xujin$ pwd
    /Users/xujin/practice
    Jyoshin:practice xujin$ 
4.从键盘读取变量
    
- read var
- declare -i var（声明一个int变量）

-----
###管道命令（pipe）
仅处理标准输出。 `ls -al /etc | less`

- 基本读取命令：less,more,head,tail，均可作为接受标准输入的管道命令。     
- 选取命令：cut，grep    
- 排序命令：sort，wc（计数），uniq（剔除重复）
- 双向重定向：tee


###正则表达式
