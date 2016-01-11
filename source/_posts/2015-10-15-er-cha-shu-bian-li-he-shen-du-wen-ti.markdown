---
layout: post
title: "二叉树遍历和深度问题"
date: 2015-10-15 19:37
comments: true
categories: Algorithm
---


二叉树遍历包括前序，中序，以及后序遍历，主要有两种解法：递归，深度优先的非递归算法。


<!--more-->

{% codeblock lang:java %}
import java.util.Stack;

/*
 *        1
 *       / \
 *      2   3
 *     /    /
 *    4    5
 *   / \
 *  6   7
 *   \
 *    8
 *    
 *    遍历此二叉树，并求最大深度
 */

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class BinaryTreeDepth {
	public static void main(String...args){
		TreeNode root = new TreeNode(1);
		TreeNode n2 = new TreeNode(2);
		TreeNode n3 = new TreeNode(3);
		TreeNode n4 = new TreeNode(4);
		TreeNode n5 = new TreeNode(5);
		TreeNode n6 = new TreeNode(6);
		TreeNode n7 = new TreeNode(7);
		TreeNode n8 = new TreeNode(8);
		root.left = n2;
		root.right = n3;
		n2.left = n4;
		n3.left = n5;
		n4.left = n6;
		n4.right = n7;
		n6.right = n8;
		
		
		System.out.print("递归前序:");
		preorderTraversal(root);
		System.out.print(" 非递归前序:");
		preorderTraversal_NoRecursive(root);
		
		System.out.print("\n递归中序:");
		inorderTraversal(root);
		System.out.print(" 非递归中序:");
		inorderTraversal_NoRecursive(root);
		
		System.out.print("\n递归后序:");
		postorderTraversal(root);
		System.out.print(" 非递归后序:");
		postorderTraversal_NoRecursive(root);
		
		System.out.print("\n递归求二叉树的最大深度:" + maxDepth(root));
		System.out.println(" 非递归求二叉树的最大深度:" + maxDepthDFS(root));
	}
	
	
	
	 /******************遍历问题**********************/
	//递归的前序遍历
    public static void preorderTraversal(TreeNode root) {
       if(root == null) return;
       System.out.print(root.val);
       preorderTraversal(root.left);
       preorderTraversal(root.right);
    }
    //递归的中序遍历
    public static void inorderTraversal(TreeNode root) {
        if(root == null) return;
        inorderTraversal(root.left);
        System.out.print(root.val);
        inorderTraversal(root.right);
    }
    //递归的后序遍历
    public static void postorderTraversal(TreeNode root) {
        if(root == null) return;      
        postorderTraversal(root.left);
        postorderTraversal(root.right);
        System.out.print(root.val);
    }
	
    //非递归的前序遍历，深度优先，用栈
    public static void preorderTraversal_NoRecursive(TreeNode root) {
        Stack<TreeNode> stack = new Stack<TreeNode>();
        if(root == null) return;
        //首先把左子树都压栈，边压栈边读，得到最后一个左节点，将其删除，再将其的右节点作为新的root压栈。
        while(root != null || !stack.empty()){
        	while(root != null){
        		stack.push(root);
        		System.out.print(root.val);
        		root = root.left;
        	}
        	root = stack.pop();
        	root = root.right;
        }
     }
    
    //非递归的中序遍历
    public static void inorderTraversal_NoRecursive(TreeNode root) {
    	Stack<TreeNode> stack = new Stack<TreeNode>();
        if(root == null) return;
        while(root != null || !stack.empty()){
        	while(root != null){
        		stack.push(root);
        		root = root.left;
        	}
        	root = stack.pop();
        	System.out.print(root.val);
        	root = root.right;
        }
     }
    
    //非递归的后序遍历，需要标记右子树是否被访问过
    public static void postorderTraversal_NoRecursive(TreeNode root) {
    	Stack<TreeNode> stack = new Stack<TreeNode>();
    	Stack<Integer> stag = new Stack<Integer>();//标记对应的节点的右节点是否已经被访问了
        if(root == null) return;
        while(root != null || !stack.empty()){
        	while(root != null){
        		stack.push(root);
        		stag.push(0);
        		root = root.left;
        	}
        	if(stag.peek() == 1){
        		//如果此右节点被访问过，或者其没有右节点，那么此时可以读这个节点
        		System.out.print(stack.pop().val);
        		stag.pop();
        		root = null;
        	}else{
        		root = stack.peek();
        		root = root.right;
        		stag.pop();
        		stag.push(1);
        	}
        }
     }
    
    
    
    
    
    
    /******************最大深度问题**********************/
    
	//递归求最大深度
    public static int maxDepth(TreeNode root) {
       if(root == null) return 0;
       int left = maxDepth(root.left) + 1;
       int right = maxDepth(root.right) + 1;
       return left < right ? right : left;
    }
	
    //深度优先搜索求最大深度，利用了后序遍历，每次输出的时候计算栈的大小
    public static int maxDepthDFS(TreeNode root) {
    	Stack<TreeNode> stack = new Stack<TreeNode>();
    	Stack<Integer> stag = new Stack<Integer>();//标记右子树是否被访问过
    	int treeDeep = 0;
    	while(root != null || !stack.isEmpty()){
    		//把左子树压栈
    		while(root != null){
    			stack.push(root);
    			stag.push(0);
    			root = root.left;
    		}
    		
    		if(stag.peek() == 1){//如果该节点的右子树被访问过了
    			treeDeep = Math.max(treeDeep, stack.size());
    			stack.pop();
    			stag.pop();
    			root = null;
    		}else{
    			root = stack.peek();
    			root = root.right;
    			stag.pop();
    			stag.push(1);//节点的右子树被访问过了，标记置为1
    		}
    	}
    	return treeDeep;
    }

}

{% endcodeblock %}
