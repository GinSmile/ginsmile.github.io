---
layout: post
title: "两个栈实现一个队列"
date: 2015-09-30 21:23
comments: true
categories: Algorithm
---


两个栈实现一个队列是很经典的一道题目，LeetCode原文为：     

>
> Implement the following operations of a queue using stacks.
>
* push(x) -- Push element x to the back of queue.
* pop() -- Removes the element from in front of queue.
* peek() -- Get the front element.
* empty() -- Return whether the queue is empty.

<!--more-->
>
Notes:
>
* You must use only standard operations of a stack -- which means only push to top, peek/pop from top, size, and is empty operations are valid.
* Depending on your language, stack may not be supported natively. You may simulate a stack by using a list or deque (double-ended queue), as long as you use only standard operations of a stack.
* You may assume that all operations are valid (for example, no pop or peek operations will be called on an empty queue).

即为用两个栈s1,s2实现一个队列，我目前想到的有两种方法。

###解法一
第一种是把s1看作队列。四种操作里面仅仅改变push(x)操作。push(x)的具体操作为：

* 先把所有元素从s1中移到s2
* 然后将元素x用栈的push操作进入s1
* 之后再把s2中的元素push到s1

这样，s1的栈底始终是队列的尾部，栈顶是队列头部。对s1这个栈的pop(),peek(),empty()即为对队列的相应操作。
时间复杂度2n^2。

{% codeblock lang:java %}
class MyQueue {
    Stack<Integer> s1 = new Stack<Integer>();//peak,pop,empty
    Stack<Integer> s2 = new Stack<Integer>();//push中转
    
    // Push element x to the back of queue.
    public void push(int x) {
        while(!s1.empty()){
            s2.push(s1.peek());
            s1.pop();
        }
        
        s1.push(x);
        
        while(!s2.empty()){
            s1.push(s2.peek());
            s2.pop();
        }
        
    }

    // Removes the element from in front of queue.
    public void pop() {
        s1.pop();
    }

    // Get the front element.
    public int peek() {
        return s1.peek();
    }

    // Return whether the queue is empty.
    public boolean empty() {
        return s1.empty();
    }
}
{% endcodeblock %}

###解法二
第二种是把s1和s2合在一起看成一个队列。用形象化的语言来描述就是，把栈s1放到左边，栈s2放到右边。为了方便理解，我特意画了一幅图，如下所示，显示了用两个栈实现的队列的pop的过程。

* 注意，s1的栈底在左边，s2的栈底在右边。
* 注意，队列的队首在左边。




{% img center /images/pic/stack_pop.jpg 530 530 'stack' 'stack' %}


* 上图中维护了一个队列［12，11，2］，要pop，就必须把队首元素12拿出来。为此把所有元素移到s2，然后pop。
* 同里，push（x），需要把x放到队尾，最终要得到［12，11，2，x］的结果。为此，要把s2中的所有元素移到s1，然后s1.push(x)。



{% codeblock lang:java %}
class MyQueue {
    Stack<Integer> s1 = new Stack<Integer>();//peak,pop,empty
    Stack<Integer> s2 = new Stack<Integer>();//push中转
    
    // Push element x to the back of queue.
    public void push(int x) {
        while(!s2.empty()){
            s1.push(s2.peek());
            s2.pop();
        }
        s1.push(x);                
    }

    // Removes the element from in front of queue.
    public void pop() {
        while(!s1.empty()){
            s2.push(s1.peek());
            s1.pop();
        }
        s2.pop();
    }

    // Get the front element.
    public int peek() {
        while(!s1.empty()){
            s2.push(s1.peek());
            s1.pop();
        }
        return s2.peek();
    }

    // Return whether the queue is empty.
    public boolean empty() {
        return s1.empty() && s2.empty();
    }
}
{% endcodeblock %}


##扩展  两个队列实现一个栈
同样有两种方法，第一种同上，就不赘述了。代码如下：

{% codeblock lang:java %}
class MyStack {
    Queue<Integer> q1 = new LinkedList<Integer>();
    Queue<Integer> q2 = new LinkedList<Integer>();
    // Push element x onto stack.
    public void push(int x) {
        while(q1.peek() != null){
            q2.add(q1.poll());
        }
        
        q1.add(x);
        
        while(q2.peek() != null){
            q1.add(q2.poll());
        }
    }

    // Removes the element on top of the stack.
    public void pop() {
        q1.poll();
    }

    // Get the top element.
    public int top() {
        return q1.peek();
    }

    // Return whether the stack is empty.
    public boolean empty() {
        return q1.peek() == null;
    }
}
{% endcodeblock %}


第二种和上面的思路也相近。只要注意一下队首的位置，以及栈顶的位置就好。

* 栈顶在左边，两个队列的队首也在左边。

{% img center /images/pic/stack_push.jpg 530 530 'stack' 'stack' %}

{% codeblock lang:java %}
class MyStack {
    Queue<Integer> q1 = new LinkedList<Integer>();
    Queue<Integer> q2 = new LinkedList<Integer>();
    // Push element x onto stack.
    public void push(int x) {
        if(q1.peek() != null){
            q2.add(x);
            while(q1.peek() != null){
                q2.add(q1.poll());
            }
        }else{
            q1.add(x);
            while(q2.peek() != null){
                q1.add(q2.poll());
            }
        }
        
    }

    // Removes the element on top of the stack.
    public void pop() {
        if(q1.peek() != null)
            q1.poll();
        else
            q2.poll();
    }

    // Get the top element.
    public int top() {
        if(q1.peek() != null)
            return q1.peek();
        else
            return q2.peek();
    }

    // Return whether the stack is empty.
    public boolean empty() {
        return q1.peek() == null && q2.peek() == null;
    }
}
{% endcodeblock %}