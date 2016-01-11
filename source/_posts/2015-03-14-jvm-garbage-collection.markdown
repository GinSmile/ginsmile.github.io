---
layout: post
title: "JVM Garbage Collection"
date: 2015-03-14 08:40
comments: true
categories: Java
---

首先梳理一下Java中各种对象、变量存储的位置。*参见后面程序例子*
	
	
*	Register（寄存器，位于处理器内部）：最快的存储区，由编译器根据需求进行分配，不能直接控制。
*	Stack（栈，位于RAM区）：存放基本类型的变量数据和对象的引用。对于**小的、简单的变量**，Java创建一个并非是引用的“自动”变量，这个变量直接存储“值”，并置于栈中。例如，`int a = 3;`，a和3均在栈中。
*	Heap（堆，位于RAM区）：**所有的new对象**存储在堆中。例如，`String str = new String("something")；`，str在栈中，something在堆中。
*	常量存储（在代码内部，或者放在ROM中）：为了避免某个常量值被反复实例化，存放可以在编译期就确定的字符串常量、基本类型常量以及基本类型的包装类（Byte, Short, Integer, Long, Character, Boolean，并且对应值小于等于127时），例如：
	* `String str = "something"`，str在栈中，“something”在String类的常量池中。
	* `Integer i1=127`，当且仅当包装器要包装的值小于127才能用常量池。
	* `Integer i2=new Integer(12);`没用包装器，i2在栈中，12在堆中
	* `public static int a = 1；`，a这个变量名在栈中，1在常量池中
*	非RAM存储：流对象和持久化对象。在流对象中，对象转化成字节流，通常被发送给另一台机器。在持久化对象中，对象被存放在磁盘上。
<!--more-->


>
**注意：基本类型和引用类型，它们的变量都是存放在栈中，包括类型、名称、值。只不过基本类型变量的值为一个具体的值，而引用类型的变量的值为对象在堆中的地址。**	    
另外，一个类的名字，一个类字段的名字/所属类型，一个类方法的名字/返回类型/参数名与所属类型，也会存在常量池中。（详见[常量池](http://baike.baidu.com/view/8680346.htm)）


栈和常量池中的内容会共享！！比如，

1.	`int a ＝ 3; int b = 3`       
编译器首先会在栈中创建一个变量为a的引用，然后查找有没有字面值为3的地址，没找到，就开辟一个存放3这个字面值的地址，然后将a指向3的地址	。之后，在栈中创建一个变量为b的引用，查找值3的地址，找到，将b指向3的地址。
2.	`String s1 = "somestr"; String s2 = "somestr";`      
与上面同理，编译器首先会在栈中创建一个变量为s1的引用，然后在常量池中查找有没有“somestr”的地址，没找到，就在常量池在存放“somestr”，然后将s1指向“somestr”的地址。之后，在栈中创建一个变量为s2的引用，查找值“somestr”的地址，找到，将b指向“somestr”的地址。

下面给出一个例子，可以根据这个例子对以上内容有更清楚的理解。
	
	package com.xujin.fun;

	public class Student{
		//****************关于带static的**************************************
		public static int total = 0;//total这个变量名在栈中，total的值0存放在常量池，所有带static的变量的值都存放在本类Student常量池
		public static String schoolName = "Cook School";//schoolName这个变量名在栈中，"Cook School"在String类的常量池

		
		//****************关于普通情况**************************************
		public Toy toy;//学生的玩具，toy这个变量名在栈中，所有new出来的对象在堆中。
		public String name;//字符串常量，name这个变量名在栈中，之后的赋值比如“july”存在于String类的常量池中
		public int age;//基本类型常量，age在栈中，在运行后的赋值也在栈中
		public int id;
		
		public Student(String name, int age, int id){
			this.total++;
			this.name = name;
			this.age = age;
			this.id = id;
			this.toy = new Toy(name+"'s toy!", age*3);
		}
		
		public static void main(String...args){
			Student st1 = new Student("john", 12, 1);
			Student st2 = new Student("july", 12, 2);
			Student st3 = new Student("jimm", 13, 3);
			System.out.println(st1.name);
			System.out.println(st1.id);
			System.out.println(st1.age);
			System.out.println(Student.total);
			System.out.println(Student.schoolName);
			System.out.println(st1.toy.toString());
			
			
			//****************关于特例String类**************************************
			String str0 = "abc";
			String str1 = "abc"; 
			System.out.println(str1 == str0);//true，栈中存放的变量名str0和str1均指向String类的常量池的“abc”。
			
			String str2 = new String("abc");
			String str3 = new String("abc"); 
			System.out.println(str2 == str3);//false，栈中存放的变量名str2和str3指向堆中的不同地址
			System.out.println(str2.equals(str3));//true，eauals比较值而不是内存地址
			
			
			//****************关于特例Integer类等基本类型的包装器**************************************
			Integer i1 = 40;
	        Integer i2 = 40;
	        Integer i3 = 0;
	        System.out.println("i1=i2\t" + (i1 == i2));//true，i1和i2都在Integer类的常量池
	        System.out.println("i1=i2+i3\t" + (i1 == i2 + i3));//true，同上
	        
	        Integer i4 = new Integer(40);
	        Integer i5 = new Integer(40);
	        Integer i6 = new Integer(0);
	        Integer i7 = new Integer(160);
	        System.out.println("i2=i4\t" + (i2 == i4));//false，i4没有用包装器，new出来的一定在堆中
	        System.out.println("i4=i5\t" + (i4 == i5));//false，new出来的，都在堆中
	        System.out.println("i4=i5+i6\t" + (i4 == i5 + i6));  //true，Java的数学计算是在内存栈里，这里算的是40 = （40 + 0），都是基本类型的运算
	        System.out.println("i7/4=i5\t" + (i7/4 == i5));//true，同上
	        
	        Integer i8 = 128;
	        Integer i9 = 128;
	        System.out.println("i8=i9\t" + (i8 == i9)); //false，超出了-128～127这个范围，   

		}
		
		
	}


	class Toy{
		public String name;
		public int price;
		public Toy(){
			name = "commonOne";
		}
		
		public Toy(String name, int price){
			this.name = name;
			this.price = price;
		}
		public String toString(){
			return name+" "+price;
		}
	}






**Java在堆分配空间的速度，可以和其他语言在堆栈上分配空间的速度相媲美！**



Java的垃圾回收器，用来监视new创建的所有对象，并辨别那些不会再被引用的对象。随后，释放这些对象的内存空间，清除内存记录碎片，以便供其他新的对象使用。--->旨在消除`内存泄露`问题，**你永远也不需要销毁对象！！**

###1. 何时垃圾回收？
垃圾回收器优先级很低，只要程序没有濒临存储空间用完的那一刻，对象占用的空间就永远也得不到释放。因为垃圾回收器也有开销，要是不使用它，就不用支付这部分开销了！因而，**Java中的对象并非总是被垃圾回收**。

###2. 垃圾回收前，将首先自动调用finalize()方法
垃圾回收只能回收new的对象，finalize()方法就是用来处理new以外的方法（例如本地方法）创建的对象。

*	finalize()方法不能被直接调用！！！
*	System.gc()只能提高垃圾回收器的调用的可能性。

###3. 简单的引用计数（Java不用）
每个对象都含有一个引用计数器，当有引用连接到对象时引用计数加1，当引用离开作用域或被置为null时，引用计数减1。      
缺点：不能处理循环引用。

###4. “自适应的、分代的，停止-复制、标记清扫”式垃圾回收器
1.	tracing算法（标记-清扫）：从栈和静态存储区出发，利用root搜索算法，遍历所有引用，进而找出所有存活的对象（引用指向的对象）。每当找到一个就会给对象设定一个标记。全部标记完，就开始清理。因而，剩下的空间是不连续的，还要进行压缩。**适合于少量垃圾产生时**。
2.	compacting（标记-清扫-压缩）算法：在“标记-清扫”的过程中，算法将所有的对象移到堆的一端，堆的另一端就变成了一个相邻的空闲内存区，收集器会对它移动的所有对象的所有引用进行更新，使得这些引用在新的位置能识别原来的对象。**解决了碎片问题**
3.	copying算法（停止-复制）：先暂停程序的运行，然后将所有存活的对象从当前堆复制到另一个堆，没被复制的全是垃圾。这种方式效率低，但新堆结构紧凑。
4.	generation算法（分代）：将堆分成两个或多个，每个子堆作为对象的一代。由于多数对象存在的时间比较短，随着程序丢弃不使用的对象，垃圾收集器将从最年轻的子堆中收集这些对象。在分代式的垃圾收集器运行后，上次运行存活下来的对象移到下一最高代的子堆中，由于老一代的子堆不会经常被回收，因而节省了时间。
5.	adaptive算法：将上述几个算法结合的算法。	

自适应技术是多种方式结合的方法。如果Java虚拟机监视到所有对象都很稳定，垃圾回收器效率降低的话，就切换到“标记-清扫“方式；同样，如果堆空间出现许多碎片，就会切换到“停止-复制”方式。


###参考资料

*	[Java堆、栈和常量池](http://blog.csdn.net/shuaihj/article/details/10446901)
*	《Java编程思想》
*	[Java垃圾回收](http://baike.baidu.com/view/14960900.htm)
