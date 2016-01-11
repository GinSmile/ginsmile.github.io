---
layout: post
title: "matlab学习"
date: 2015-03-16 18:39
comments: true
categories: Matlab
---

matlab中的小知识点，易混知识点总结。
###常用命令

* clear
* quit
* clf  擦除figure
* who  检查内存变量    <!--more-->
* whos 检查驻留变量的详细情况
* help
* save
* load





###图形相关

在一个图上做多条曲线
	
	t = 0:0.1:4*pi
	y0 = sin(t)
	y1 = cos(t)
	y2 = 0.9.^t

	figure('numbertitle','off','name','结果图像1');
	plot(t,y0,t,y1,t,y2)%画图，以t为横坐标，y0，y1，y1分别为为纵坐标，在一个图上画出三条线
	axis([0 15 0 1]);
	saveas(gcf, 'result', 'bmp'); %保存为bmp图像
	  
	figure('numbertitle','off','name','结果图像2')
	subplot(2,2,1)%小图，在第1个位置
	plot(t,y0,'Color','R');
	subplot(2,2,2)%小图，在第2个位置
	plot(t,y1,'Color','B');
	subplot(2,2,3)%小图，在第3个位置
	plot(t,y2);

结果图如下

---
{% img center /images/pic/plot_1.png 500 500  'sina' 'sina' %}
{% img center /images/pic/plot_2.png 500 500  'sina' 'sina' %}


---

###数据读取相关
matlab可以读取.mat，.txt等文件，使用以下语句，读取的结果为矩阵。

	data = load(data.mat);
	data2 = load(a.txt);
读取列


	%基本形式为data(a:b, c:d),取第a～b行，和c～d列
	col1 = data(:,1) %第一列
	col23 = data(:,2:3) %第二列和第三列
	
读取行

	col1 = data(1:500,:) %第1～500行
	

在矩阵中取出子矩阵
	
	B =
     4     2     3
    41     5     6
     7    81     9
    10    11    12

	>> B1=B(1:2,[1 3])

	B1 =
 	    4     3
 	   41     6
	
	

###矩阵相关
分号`;`表示换行，**`[]`中括号，用来构建矩阵或者数组**
	
	>> A=[1,2,3;4,5,6;7,8,9;10,11,12]	
		A =
        1     2     3
        4     5     6
        7     8     9
        10    11    12
	
矩阵元素输入，**`()` 小括号，用于引用数组的元素**
	
	>> A(1,1) = 2	
		A =
        2     2     3
        4     5     6
        7     8     9
        10    11    12
	
点乘，就是每个元素都相乘
	
	>> A.*B

	ans =

     8     4     9
    164    25    36
    49   648    81
    100   121   144

###数组相关

数组的基本操作

	>> x=[1 2 3 4 5]
	x =
	     1     2     3     4     5
	     
	>> x(2)
	ans =
	     2

	>> x(2) = 33
	x =
	     1    33     3     4     5

关于**大括号`{}`，用于数组的分配或引用。**

	>> x = [1 2 3 4]
	x =
	     1     2     3     4

	>> y = [4 3 2 1]
	y =
	     4     3     2     1

	>> z = {x;y}
	z = 
	    [1x4 double]
	    [1x4 double]

	>> z{1}
	ans =
	     1     2     3     4

	>> z{2}
	ans =
	     4     3     2     1

	>> z{1,1}
	ans =
	     1     2     3     4

	>> z{2,1}
	ans =
	     4     3     2     1
	    
	>> z{2,1}(1)
	ans =
  	   4

