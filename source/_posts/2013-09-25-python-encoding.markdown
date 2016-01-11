---
layout: post
title: "Python编码转换"
date: 2013-09-25 10:37
comments: true
categories: Python
---

python中默认使用ASCII编码，但是如果出现中文等非ASCII字符就要使用其他编码方式了。定义程序的编码方式有好几种，只要满足正则表达式：

    \%^.*\(\n.*\)\?#.*coding[:=]\s*[0-9A-Za-z-_.]\+.*$                            
    
即可。最常见的是在第一行或者第二行写上字符：`# -*- coding:utf-8 -*-`  或者 ` #coding=utf-8 `

<!--more-->
其他编码转换为unicode
-----------------
使用函数unicode(s,encoding) 或者s.decode(encoding)，就可以将gb2312，utf-8，GBK等编码转换为unicode，例如将utf-8转换成unicode：
    
    # -*- coding: utf-8 -*-
    #文件保存为UTF-8格式
    s = '汉字'
    
    #utf-8转换成unicode
    s_unicode = s.decode("utf-8")  
    s_unicode2 = unicode(s,'utf-8') #第二种方法
    s_unicode3 = u'汉字' #第三种方法
    print s_unicode == s_unicode2 #True
    print s_unicode == s_unicode3 #True
unicode转化成其他编码
-----------------------------
如果字符串为ascii或者unicode编码，就可以直接使用encode方法：
    # -*- coding: UTF-8 -*-
    s_unicode = u'汉字'
    
    #utf-8转换成gbk
    s_gbk = s_unicode.encode('gbk')

若直接使用encode方法，Python会自动的先把s解码为unicode，然后再编码成一定的编码方法。
若s不是ascii编码或是unicode编码而直接使用encode方法，如下：
    # -*- coding: utf-8 -*-
    s_utf8 = '汉字'
    
    s_utf8.encode('gbk') #error
会出现以下错误：    

    UnicodeDecodeError: 'ascii' codec can't decode byte 0xe6 in position 0: ordinal not in range(128)

非unicode编码之间的转换
-------------------
这个要拿unicode当作中间编码
    # -*- coding: utf-8 -*-
    s_utf8 = '汉字'
    
    #utf-8转换成gbk
    s_gbk = s_utf8.decode('utf-8').encode('gbk')
    
    #utf-8转换成gb2312
    s_gb2312 = s_utf8.decode('utf-8').encode('gb2312')
    
    #gbk转换成gb2312
    s_gb2312_next = s_gbk.decode('gbk').encode('gb2312')
    print s_gb2312 == s_gb2312_next #True

参考资料
-----------------
> - [python编码转换](http://www.pythonclub.org/python-basic/codec)
> - [python编码注释](http://my.oschina.net/u/1178546/blog/146660)