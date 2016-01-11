---
layout: post
title: "BOP1.8 小飞的电梯调度算法"
date: 2015-09-30 13:34
comments: true
categories: [Algorithm, BOP]
---

一座大厦，里面的电梯一开始在高峰时段每层都停，现设计一种方式，让电梯只停在其中的某一层。所有的乘客从这层爬楼梯到自己的楼层。在一楼的时候，每个乘客选择自己的目的层，电梯自动计算出应该停的层。

问：电梯停在哪一层，能够保证乘坐的所有乘客爬楼梯的层数之和最少。


首先对问题进行抽象->假设楼层共N层，电梯停在第i层，要去第j层的乘客数目总数为nPerson[j]，这样，所爬楼梯的总数就是

* Σ{nPerson[j]*|i-j|}，j从1到N

<!--more-->
###一，暴力求解 
从第一层开始，依次算每层的爬楼梯总数。一个嵌套循环。


{% codeblock lang:java %}
public class BOP_1_8_elevator {
	public static void main(String...args){
		int[] nPerson = {0,1,5,7,3,2,0,0,0,8,2}; //nPerson[i]表示第i层要下的人
		int N = 10;//大楼的总层数
		int[] res = getRes(nPerson, N);
		res = getRes(nPerson, N);
		System.out.println("应停在第"+ res[0] +"层，所有人总共需要爬"+ res[1] + "层。");
	}

	private static int[] getRes(int[] nPerson, int N) {
		int nMinFloor = Integer.MAX_VALUE;
		int nTargetFloor = 0;//电梯最终要停的目标楼层
		int nFloor = 0; //如果停在第i层，总共需要爬的楼层数
		for(int i = 1; i <= N; i++){
			nFloor = 0;
			for(int j = 1; j <= N; j++){
				nFloor += nPerson[j] * Math.abs(i - j);
			}
			if(nFloor < nMinFloor){
				nMinFloor = nFloor;
				nTargetFloor = i;
			}
		}
		
		int[] res = new int[2];
		res[0] = nTargetFloor;
		res[1] = nMinFloor;
		return res;
	}

}
{% endcodeblock %}

###二，找数学规律
* 假设停在第i层，此时总的爬楼层数为Y = Σ{nPerson[j]*|i-j|}，j从1到N。
* 如果有N1个乘客在i层以下，N2个乘客在i层，N3个乘客在i层以上，那么
    * 若停在第i-1层，i层及以上都加一层，即N2 + N3， i层以下都减一层，共减去N1，故所有人总共需要爬Y - N1 + N2 + N3 
    * 若停在第i+1层，i层及以下都加一层，即N1 + N2， i层以上都减一层，共减去N3，故所有人总共需要爬Y + N1 + N2 - N3 

* 因而
    * 当N2 + N3 - N1 > 0 时，停在i-1更好，即N2 > N1 - N3
    * 当N2 + N1 - N3 < 0 时，停在i+1更好，即N2 < N3 - N1
    * 其他情况在i

所以，从第一层开始的话，必定有一个N2 + N1 - N3 = 0 的时刻，此时的i即为要求的i。

{% codeblock lang:java %}
	private static int[] getRes2(int[] nPerson, int N) {
		int nMinFloor = 0;
		int nTargetFloor = 0;//电梯最终要停的目标楼层
		int N3 = 0; //N3个乘客在i层以上
		
		//求出第一层的层数,以及N3的值
		for(int i = 2; i <= N; i++){
			N3 += nPerson[i];
			nMinFloor += nPerson[i] * Math.abs(i - 1);
		}
		
		//从第2层开始，一直往上找，找出最小
		int N1 = 0;
		int N2 = nPerson[1];
		for(int i = 2; i <= N; i++){
			if(N1 + N2 < N3){
				nTargetFloor = i;
				nMinFloor += N1 + N2 - N3;
				N1 += N2;
				N2 = nPerson[i];
				N3 -= nPerson[i];
			}else break;
		}
		
		
		int[] res = new int[2];
		res[0] = nTargetFloor;
		res[1] = nMinFloor;
		return res;
	}
{% endcodeblock %}

###三，中间数的方法

当有两个人，第一个人在第2层下，第二个人在第9层下，那在2~9中间无论哪个都可以！总共要爬的楼层一定是7层！！若nPerson是{2,1,0,0,2,2}，等同于去掉一个最高层和最低层的{1,1,0,0,2,1}的结果，！

过程如下：

    * {0,2,1,1,4,2,2}
    * {0,1,1,1,4,2,1}
    * {0,0,1,1,4,2,0}
    * {0,0,0,1,4,1,0}
    * {0,0,0,0,4,0,0} 得到应该停在第4层


{% codeblock lang:java %}
	private static int[] getRes3(int[] nPerson, int N) {
		int left=1,right=N,nFloor = 0;
		int[] nPersonClone = nPerson.clone();
		while(right-left>0)//模拟 
		{
			while(nPerson[left]==0) 
				left++;
			nPerson[left]--;
			while(nPerson[right]==0)
				right--;
			nPerson[right]--;
		}
		int[] res = new int[2];
		res[0] = left;
		
		//计算第left层时候的要爬的总层数
		for(int j = 1; j <= N; j++){
			nFloor += nPersonClone[j] * Math.abs(left - j);
		}
		
		res[1] = nFloor;
		return res;
	}
{% endcodeblock %}
###扩展，最小能量
若往上走耗费k单位能量，往下走耗费1单位能量，那如何解决。

只需要将公式稍微变一下就好。
* 若停在第i-1层，所有人总共需要爬 Y - N1 + (N2 + n3) * k 
* 若停在第i+1层，所有人总共需要爬 Y + N1 + N2 - N3 * k 

###扩展，可以停k个楼层
to be continue...


