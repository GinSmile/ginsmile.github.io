---
layout: post
title: "递归与分治问题"
date: 2015-06-17 00:17
comments: true
categories: Algorithm
---


递归
----
递归就是自己调用自己，最基本的算法。	
构成递归需要具备的条件为：

1. 子问题须与原始问题为同样的事，且更为简单；
2. 不能无限制地调用本身，须有个出口，化简为非递归状况处理。

递归相关的经典问题主要有：汉诺塔问题，斐波那契数列，枚举排列等。

<!--more-->


#####1. 汉诺塔问题
问题描述:		
假设有n个圆盘，三根柱子a，b，c，需要把n个盘子（从下往上按照大小顺序摞着）从a柱移动到c柱，在小圆盘上不能放大圆盘，在三根柱子之间一次只能移动一个圆盘。	
解决办法:	

1. 步骤	
解法的基本思想是递归。先把A塔顶部的n-1块盘移动到b塔，再把A塔剩下的大盘移到c，最后把b塔的n-1块盘移到c。每次移动多于一块盘时，则再次使用上述算法来移动。

2. 移动次数			
假设移动n个盘子需要移动f(n)次，所以把n-1个盘子移动到b柱子上，需要移动f(n-1)次，然后把第n个盘子移动到c柱子上，需要移动1次，最后把n-1个盘子移动到c柱子上，需要移动f(n-1)次。
综上所述，一共移动了
        f(n) = 2 f(n-1) + 1
    因而，若有n个盘子，则至少需要2^n - 1次移动


{% codeblock lang:java %}
import java.util.Scanner;

public class HannoiTower {
	static Scanner reader=new Scanner(System.in);
	public static void main(String...args){
		while(true){
			System.out.println("请输入汉诺塔的盘子总数：");
			int n = reader.nextInt();
			if(n < 1){
				System.out.println("输入的数字必须大于0");
			}
			System.out.println("共需要" + (int)(Math.pow(2, n) - 1) +"次移动：");
			hannoi(n, 'a','b','c');
		}
		
	}
	
	public static void hannoi(int n, char a, char b, char c){
		//把n个盘子从a柱子移动到b柱子   
	    if(n > 0) {  
	        hannoi(n - 1, a, c, b);// 把n-1个盘子移动到c柱子上  
	        move(a, b);  // 把a移动到b   
	        hannoi(n - 1, c, b, a);  // 把第n-1个盘子从c柱子移动到b柱子上  
	    }  
	}

	private static void move(char a, char b) {
		// TODO Auto-generated method stub
		System.out.println(a + "->" + b);
	}
}
{% endcodeblock %}

##### 2. Fibonacci数列
斐波那契数列指的是这样一个数列 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233，377，610，987，1597，2584，4181，6765，10946，17711，28657，46368
自然中的斐波那契数列
自然中的斐波那契数列
特别指出：第0项是0，第1项是第一个1。
这个数列从第2项开始，每一项都等于前两项之和。

因尔，其递推公式为：

* F(n) = 1;  when n == 0,1
* F(n) = F(n-1)+F(n-2); when n > 1

{% codeblock lang:java %}
//输出第n个Fibonacci数列
public class Fibonacci {
	public static void main(String...args){
		System.out.println(fibonacci(10));
	}	
	public static int fibonacci(int n){
		if(n == 0 || n == 1){
			return 1;
		}else{
			return fibonacci(n - 1) + fibonacci(n - 2);
		}
		
	}
}
{% endcodeblock %}

##### 3. 全排列问题
给定一个数组arr，输出其全排列。

解法：	
要用到递归，伪代码如下：
	
	//arr为原始数组，res为一个排列，cur为在res中的当前位置，n为arr大小，
	permu(arr, res, cur, n){
		if cur == n 
			输出结果res
		else {
			遍历arr的每一个元素arr[i]{
				如果arr[i]没有被res用过{
				res[cur] = arr[i];
				permu(arr, res, cur + 1, n);
			}
		}
	}

{% codeblock lang:java %}
public class Permutation {
	public static void main(String...args){
		int[] arr = {1, 2, 3};
		int[] res = new int[arr.length];
		permu(arr, res, 0, arr.length);
	}

	private static void permu(int[] arr, int[] res, int cur, int n) {
		int i = 0, j = 0;
		if(cur == n){
			for(i = 0; i < n; i++){
				System.out.print(res[i] + ", ");
			}
			System.out.println();
		}else{
			for(i = 0; i < n; i++){//按索引从小到大，依次尝试arr的每个元素
				boolean isAppear = false;
				for(j = 0; j < cur; j++){
					if(res[j] == arr[i]){
						isAppear = true;
						break;
					}
				}
				
				if(!isAppear){
					res[cur] = arr[i];
					permu(arr, res, cur + 1, n);
				}				
			}
		}
	}
}
{% endcodeblock %} 


分治
----
分治(divide and conquer)，是基于多分枝递归的一种算法。
分治法的基本思想是将一个规模为n的问题分解为k个规模较小的子问题，这些子问题互相独立且与原问题相同。递归地解这些子问题，然后将各子问题的解合并得到原问题的解。

分治法是递归的一种，经典的问题有：归并排序，循环日程表等


##### 1. 循环日程表



