---
layout: post
title: "左旋字符串"
date: 2015-08-31 21:35
comments: true
categories: Algorithm
---

问题为：
> 将一个n元一维向量向左旋转i个位置。例如，当n＝8，i＝3时候，向量x=abcdefgh，旋转为defgh*abc*。如何用最少的时间和空间完成？

<!--more-->
方案1 使用临时数组复制
-----
此方法的过程为如下，缺点是需要一个大小为i的临时数组

* 将x的前i个元素复制到临时数组中
* 将余下的n-i个元素向左移动i个位置
* 将最初的i个元素从临时数组中复制到x中余下的位置

具体代码如下：
{% codeblock lang:java %}
public class Zuoxuan {
	public static void main(String...args){
		String x = "abcdefgh";
		int i = 3;
		String reString = doZuoxuan(x, i);
		System.out.println(reString);
	}

	private static String doZuoxuan(String x, int i) {
		//将x的前i个元素复制到临时数组中
		char[] xChars = x.toCharArray();		
		char[] newTemp = new char[i];
		for(int c=0; c<i; c++){
			newTemp[c] = xChars[c];
		}
		
		//将余下的n-i个元素向左移动i个位置		
		for(int c=i; c<x.length(); c++){
			xChars[c-i] = xChars[c];
		}
		
		//将最初的i个元素从临时数组中复制到x中余下的位置
		for(int c=x.length()-i, j=0; c<x.length(); c++){
			xChars[c] = newTemp[j++];
		}
		
		return String.valueOf(xChars);
		
	}
}
{% endcodeblock %}




方案2 构造左旋一位的函数
----
此方法如下，缺点是有过多的操作，运行时间长

* 定义一个函数，可以左旋一位
* 调用这个函数i次

具体代码如下：
{% codeblock lang:java %}
/* 定义一个函数，可以左旋一位
* 调用这个函数i次
*/
public class ZuoxuanOneByOne {
	public static void main(String...args){
		String x = "abcdefgh";
		int i = 3;
		for(int j=0; j<3; j++){
			x = doZuoxuanOne(x);
		}
		System.out.println(x);
	}

	private static String doZuoxuanOne(String x) {
		//将x的第一个元素复制到临时变量中
		char[] xChars = x.toCharArray();
		char tempChar = xChars[0];
		
		
		//将余下的n-1个元素向左移动1个位置		
		for(int c=1; c<x.length(); c++){
			xChars[c-1] = xChars[c];
		}
		
		//将第一个元素从临时变量中复制到x最后一位
		xChars[x.length()-1] = tempChar;
		return String.valueOf(xChars);
		
	}
}
{% endcodeblock %}

方案3 每隔i个元素跳跃移动
----
这个方法比较巧妙，很容易出错，步骤如下：

* 移动x[0]到临时变量t，然后移动x[i]到x[0],x[2i]到x[i]...以此类推(将所有下标对n取模)，直至返回到取x[0]中的数据，此时改为从t取值然后终止过程
* 如果该过程没有置换全部元素，那再从x[1]开始～
* 一共置换gcd(n,i)次！！！n和i的最大公约数！


{% codeblock lang:java%}
//跳跃着移动

public class ZuoxuanTiaoyue {
	public static void main(String...args){
		String x = "abcdefg";//n=7
		int i = 3;
		String reString = doZuoxuan(x, i);
		System.out.println(reString);
	}

	private static String doZuoxuan(String x, int i) {
		char[] xChars = x.toCharArray();		
		char temp;
		int n = x.length();
		
		//一共进行gcd(n,i)次循环,7和3的最大公约数为1，故就循环一次就可以全部置换
		for(int j=0; j<gcd(n,i); j++){
			temp = xChars[j];
			//一次循环移动一批数据，按如下顺序置换，
			//xChars[0],xChars[3],xChars[6],xChars[2],xChars[5],xChars[1],xChars[4],xChars[0]...
			for(int m=j;;){			
				xChars[m] = xChars[(m + i) % n];
				if((m + i) % n == j){
					xChars[m] = temp;
					break;
				}
				m = (m + i) % n;
			}
			
		}
		
		
		return String.valueOf(xChars);
		
	}

	public static int gcd(int p, int q){
		if(q == 0) return p;
		int r = p % q;
		return gcd(q, r);
	}

	
	
}

{% endcodeblock %}





方案4 用翻转代替左旋
----
这是最巧妙最简单的一种方法，利用两次翻转，漂亮的完成了左旋。

* reverse(0,i-1); // cbadefgh
* reverse(i,n-1); // cbahgfed
* reverse(0,n-1); // defghabc

具体代码如下：
{% codeblock lang:java%}
//两次翻转
public class ZuoxuanFanzhuan {
	public static void main(String...args){
		String x = "abcdefgh";
		int i = 3;
		int n = x.length();
		x = reverse(x, 0,i-1); // cbadefgh
	    x = reverse(x, i,n-1); // cbahgfed
		x = reverse(x, 0,n-1); // defghabc
		System.out.println(x);
	}

	//翻转x[l]~x[r]
	private static String reverse(String x, int l, int r) {
		char[] xChars = x.toCharArray();
		int len = r-l+1;
		char temp;
		for(int i=l; i<l+len/2; i++){
			temp = xChars[i];
			xChars[i] = xChars[r-(i-l)];
			xChars[r-(i-l)] = temp;
		}
		return String.valueOf(xChars);
	}
}

{% endcodeblock %}