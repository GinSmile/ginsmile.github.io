---
layout: post
title: "BOP1.15 数独问题"
date: 2015-10-28 00:05
comments: true
categories: [Algorithm, BOP]
---


####1，构造数独
有两种方式可以用来构造数独。

#####1.1， 置换法
(1) 首先生成一个3*3的任意矩阵B5	
(2) 把整个数独的各个小矩阵命名为B1....B9	
 
 B1 B2 B3	
 B4 B5 B6	
 B7 B8 B9	
 <!--more-->

(3)，通过置换列的方法，利用B5，把B2和B8填好	
(4)，通过置换行的方法，利用B5，把B4和B6填好	
(5)，通过置换列的方法，利用B4，把B1和B7填好		
(6)，通过置换列的方法，利用B6，把B3和B9填好 	 

	*                i g h|c a b|f d e
	*                c a b|f d e|i g h
	*                f d e|i g h|c a b
	*                -----------------
	*                g h i|a b c|d e f
	*                a b c|d e f|g h i
	*                d e f|g h i|a b c
	*                -----------------
	*                h i g|e f d|b c a
	*                b c a|h i g|e f d
	*                e f d|b c a|h i g

这种方法比较简单，难点主要在复制的时候要注意各个索引的大小。

{% codeblock lang:java%}
import java.util.ArrayList;
import java.util.Random;

public class BOP_1_15_Sudoku {
	public static int WIDTH = 9;
	public static void main(String...args){
		CopyOfBOP_1_15_Sudoku sudoku = new CopyOfBOP_1_15_Sudoku();
		sudoku.generateValidMatrix();//置换法，生成数独的初始状态

	}

	/*-----------------------------利用小矩阵生成大矩阵的方式生成数独初始状态---------------------------*/
	private int[][] generateValidMatrix() {
		int[][] matrix = new int[WIDTH][WIDTH];
		
		//generate an random vector,named smallV,[1,2,3,4,5,6,7,8,9]
		int[] smallV = new int[WIDTH];
		for(int i = 0; i < smallV.length; i++){
			smallV[i] = i + 1;
		}
		Random ran = new Random();
		for(int i = 0; i < smallV.length; i++){
			int randomIndex = ran.nextInt(WIDTH);
			int temp = smallV[i];
			smallV[i] = smallV[randomIndex];
			smallV[randomIndex] = temp;
		}

		
		//映射到matrix的相应的B4位置上
		for(int i = 0; i < 9; i++){
			matrix[3 + i/3][i%3 + 3] = smallV[i];
		}
		
		
		/*
		 * 填B2
		 */
		for(int i = 0; i < 3; i++){
			matrix[i][3] = matrix[i + 3][5];
			matrix[i][4] = matrix[i + 3][3];
			matrix[i][5] = matrix[i + 3][4];
		}
		
		//填B8
		for(int i = 6; i < 9; i++){
			matrix[i][3] = matrix[i - 3][4];
			matrix[i][4] = matrix[i - 3][5];
			matrix[i][5] = matrix[i - 3][3];
		}
		
		
		/*
		 * 填B4
		 */
		for(int i = 0; i < 3; i++){
			matrix[3][i] = matrix[5][i + 3];
			matrix[4][i] = matrix[3][i + 3];
			matrix[5][i] = matrix[4][i + 3];
		}
		
		//填B6
		for(int i = 6; i < 9; i++){
			matrix[3][i] = matrix[4][i - 3];
			matrix[4][i] = matrix[5][i - 3];
			matrix[5][i] = matrix[3][i - 3];
		}
		
	
		
		/*
		 *填B1
		 */
		for(int i = 0; i < 3; i++){
			matrix[0][i] = matrix[2][i + 3];
			matrix[1][i] = matrix[0][i + 3];
			matrix[2][i] = matrix[1][i + 3];
		}
		
		//填B3
		for(int i = 6; i < 9; i++){
			matrix[0][i] = matrix[1][i - 3];
			matrix[1][i] = matrix[2][i - 3];
			matrix[2][i] = matrix[0][i - 3];
		}
		
		
		/*
		 *填B7
		 */
		for(int i = 0; i < 3; i++){
			matrix[6][i] = matrix[8][i + 3];
			matrix[7][i] = matrix[6][i + 3];
			matrix[8][i] = matrix[7][i + 3];
		}
		
		//填B9
		for(int i = 6; i < 9; i++){
			matrix[6][i] = matrix[7][i - 3];
			matrix[7][i] = matrix[8][i - 3];
			matrix[8][i] = matrix[6][i - 3];
		}
		
		System.out.println("最开始生成的终盘状态:");
		printMatrix(matrix);
		return matrix;
	}
	
	//打印数独
	public void printMatrix(int[][] matrix){
		System.out.println();
		for(int i = 0; i < matrix.length; i++){
			for(int j = 0; j < matrix[0].length; j++){
				if(matrix[i][j] == 0) 
					System.out.print("* ");
				else
					System.out.print(matrix[i][j] + " ");
			}
			System.out.println();
		}
	}	
}

{% endcodeblock%}
####1.2，深度优先搜索
从左上到右下依次找到合法的数字。即回溯的方法。

{% codeblock lang:java%}
/*-----------------------------用深度优先搜索算法(回溯)，生成数独初始状态---------------------------------------*/
	private int[][] generateValidMatrix_DFS() {
		int[][] matrix = new int[WIDTH][WIDTH];
		int x = 0, y = 0;
		return generateSearch(matrix, x, y);
	}

	public static boolean isFound = false;
	private int[][] generateSearch(int[][] matrix, int x, int y) {
			
			if(x == WIDTH){
				printMatrix(matrix);
				isFound = true;
				return matrix;
			}
			ArrayList<Integer> arr = getAvailableNums(matrix, x, y);
			//依次将arr中的数字填入matrix[x][y]，填完这一个再搜索下一个位置，直到找到matrix全满的第一个合法的状态（count==1）。
			for(int i = 0; i < arr.size(); i++){		
				int index = new Random().nextInt(arr.size());
				int num = arr.remove(index); //此num即为填在xy处的数字
				matrix[x][y] = num;
				int nextX = 0, nextY = 0;
				if(y == WIDTH - 1){//最后一行，换行
					nextX = x + 1;
					nextY = 0;
				}else{
					nextX = x;
					nextY = y + 1;
				}
				
				generateSearch(matrix, nextX, nextY);
				if(isFound == true) return matrix; //如果找到一个解就行了，不往下搜索了
				matrix[x][y] = 0;
			}
			return null;
	}
{% endcodeblock %}

####2，数独的解
用深度优先搜索的方法，搜索数独的所有解。和上面的生成初始状态的思路一样。首先把所有的空的格子的坐标都找到。然后一个空格一个空格地用回溯找。

下面为用深度优先搜索生成以及搜索解的算法。
{% codeblock lang:java %}
import java.util.ArrayList;
import java.util.Random;


public class BOP_1_15_Sudoku {
	public static int WIDTH = 9;
	public static void main(String...args){
		CopyOfBOP_1_15_Sudoku sudoku = new CopyOfBOP_1_15_Sudoku();
		int[][] matrix = sudoku.generateValidMatrix_DFS();//深度优先法，生成数独的初始状态	
		sudoku.dropSomeNum(matrix);//挖去几个数
		sudoku.solveSudoku(matrix);//深度优先搜索解答数独问题
		
	}
	//挖去几个数，将其赋值为0
	private void dropSomeNum(int[][] matrix) {
		int count = 53,x,y;
		Random rand = new Random();
		for(int i = 0; i < count ; i++){
			x = rand.nextInt(9);
			y = rand.nextInt(9);
			matrix[x][y] = 0;//空格在数组matrix里面用0表示
		}
		
		System.out.println("\n去掉一些数之后：");
		printMatrix(matrix);
	}
	
	/*-----------------------------用深度优先搜索算法(回溯)，生成数独初始状态---------------------------------------*/
	private int[][] generateValidMatrix_DFS() {
		int[][] matrix = new int[WIDTH][WIDTH];
		int x = 0, y = 0;
		return generateSearch(matrix, x, y);
	}

	public static boolean isFound = false;
	private int[][] generateSearch(int[][] matrix, int x, int y) {
			
			if(x == WIDTH){
				printMatrix(matrix);
				isFound = true;
				return matrix;
			}
			ArrayList<Integer> arr = getAvailableNums(matrix, x, y);
			//依次将arr中的数字填入matrix[x][y]，填完这一个再搜索下一个位置，直到找到matrix全满的第一个合法的状态（count==1）。
			for(int i = 0; i < arr.size(); i++){		
				int index = new Random().nextInt(arr.size());
				int num = arr.remove(index); //此num即为填在xy处的数字
				matrix[x][y] = num;
				int nextX = 0, nextY = 0;
				if(y == WIDTH - 1){//最后一行，换行
					nextX = x + 1;
					nextY = 0;
				}else{
					nextX = x;
					nextY = y + 1;
				}
				
				generateSearch(matrix, nextX, nextY);
				if(isFound == true) return matrix; //如果找到一个解就行了，不往下搜索了
				matrix[x][y] = 0;
			}
			return null;
	}
	
	private ArrayList<Integer> getAvailableNums(int[][] matrix, int x, int y){
		ArrayList<Integer> arr = new ArrayList<Integer>();
		for(int i = 1; i <= WIDTH; i++){
			if(isAvailable(matrix, x, y, i)){
				arr.add(i);
			}
		}
		return arr;
	}
	
	//给matrix[x][y]赋值number是否合法
	private boolean isAvailable(int[][] matrix, int x, int y, int number) {
		for(int i = 0; i < 9; i++){
			if(matrix[x][i] == number || matrix[i][y] == number){
				return false;
			}
		}

		int kx = x - x%3;
		int ky = y - y%3;
		for(int i = kx; i < kx + 3; i++){
			for(int j = ky; j < ky + 3; j++){
				if(matrix[i][j] == number){
					return false;
				}
			}
		}
		
		return true;
	}

	
	
	//打印数独
	public void printMatrix(int[][] matrix){
		System.out.println();
		for(int i = 0; i < matrix.length; i++){
			for(int j = 0; j < matrix[0].length; j++){
				if(matrix[i][j] == 0) 
					System.out.print("* ");
				else
					System.out.print(matrix[i][j] + " ");
			}
			System.out.println();
		}
	}
	
	/*---------------------------用深度优先算法得到数独问题的所有的解--------------------------------------------*/
	class space{
		int x;
		int y;
		public space(int x, int y){
			this.x = x;
			this.y = y;
		}
	}
	
	private void solveSudoku(int[][] matrix) {
		ArrayList<space> spaceArr = new ArrayList<space>();
		for(int i = 0; i < WIDTH; i++){
			for(int j = 0; j < WIDTH; j++){
				if(matrix[i][j] == 0) spaceArr.add(new space(i, j)); //因为空格在数组matrix里面用0表示
			}
		}
		
		System.out.println("\n需要填补的空格数为："+spaceArr.size());
		search(0, matrix, spaceArr);
	}

	private static int count = 0;
	private void search(int next, int[][] matrix, ArrayList<space> spaceArr) {
		if(next == spaceArr.size()){
			System.out.println("\n深度优先搜索后的第" + ++count + "个解:");
			printMatrix(matrix);
			return;
		}
		int x = spaceArr.get(next).x;
		int y = spaceArr.get(next).y;
		
		for(int i = 1; i <= 9; i++){			
			if(isAvailable(matrix ,x ,y ,i)){
				matrix[x][y] = i;
				search(next + 1, matrix, spaceArr);
				matrix[x][y] = 0;
			}
		}
	}


}

{% endcodeblock %}


上述代码的一个样例解为：

	

	6 2 8 5 4 7 1 9 3 
	9 3 5 8 1 6 7 2 4 
	1 7 4 2 3 9 6 5 8 
	5 1 9 7 2 8 3 4 6 
	3 8 2 6 9 4 5 1 7 
	4 6 7 1 5 3 9 8 2 
	2 4 1 3 6 5 8 7 9 
	8 9 3 4 7 1 2 6 5 
	7 5 6 9 8 2 4 3 1 
	
	去掉一些数之后：
	
	6 * * 5 4 7 1 * * 
	* 3 * 8 1 6 7 * * 
	* * 4 * 3 * 6 5 * 
	5 1 9 * 2 8 3 * * 
	3 8 2 6 9 4 5 1 * 
	4 * * * 5 * 9 8 2 
	2 4 1 * 6 5 * 7 * 
	* 9 3 * 7 * * * * 
	* 5 * * * 2 * * 1 
	
	需要填补的空格数为：36
	
	深度优先搜索后的第1个解:
	
	6 2 8 5 4 7 1 3 9 
	9 3 5 8 1 6 7 2 4 
	1 7 4 2 3 9 6 5 8 
	5 1 9 7 2 8 3 4 6 
	3 8 2 6 9 4 5 1 7 
	4 6 7 1 5 3 9 8 2 
	2 4 1 9 6 5 8 7 3 
	8 9 3 4 7 1 2 6 5 
	7 5 6 3 8 2 4 9 1 
	
	深度优先搜索后的第2个解:
	
	6 2 8 5 4 7 1 9 3 
	9 3 5 8 1 6 7 2 4 
	1 7 4 2 3 9 6 5 8 
	5 1 9 7 2 8 3 4 6 
	3 8 2 6 9 4 5 1 7 
	4 6 7 1 5 3 9 8 2 
	2 4 1 3 6 5 8 7 9 
	8 9 3 4 7 1 2 6 5 
	7 5 6 9 8 2 4 3 1 
