---
layout: post
title: "随机森林学习"
date: 2015-03-13 17:58
comments: true
categories: 算法
---


	随机森林算法即Random Forest，诞生于上世纪90年代的贝尔实验室。该算法可以用来做分类，聚类，回归等。属于机器学习的内容。

**Random Forest算法要点**：随机森林是一个包含多个决策树的分类器，并且其输出的类别是由个别树输出的类别的众数而定。（相当于许多不同领域的专家对数据进行分类判断，然后投票。）

<!--more-->


### 决策树的建立方法（对于分类问题）

>	
1.	用 N 来表示训练用例的个数，M表示特征数目。（注：这是建立第i棵树，原始训练集为S。）
2.	输入特征数目 m ，用于确定决策树上一个节点的决策结果；其中m应远小于M。如sqrt(M),1/2sqrt(M),2sqrt(M)等。
3.	从原始训练集S中，以有放回抽样的方式，取样N次，形成一个训练集S(i)（即bootstrap取样），作为根节点的样本，从根节点开始训练。
4.	对于每一个节点，随机选择m个特征，决策树上每个节点的决定都是基于这些特征确定的。在这 m 个特征中根据信息增益来找到该节点的最佳一维特征k及其域值th,当k<th时划到左节点，其余划到右节点。--->列采样.
5.	用未抽到的用例作预测，评估其误差。--->行采样
6.	每棵树都会完整成长而不会剪枝（Pruning），因为之前是随机采样，不会出现过拟合 (over-fitting)。

每次根据从M个特征数中随机抽取的m个特征数生成一个决策树，共生成t个不同的决策树，这些决策树通过投票对测试数据分类。

###  优点
该算法具有以下优点：

	适合做多分类问题
	训练和预测速度很快
	能一定程度容错
	避免过拟合问题
	容易实现并行化
	能有效处理大数据集
	......
	
### 例子
可以用matlab来对随机森林进行编码测试，主要利用了matlab的TreeBagger类。该类的详情请查看matlab帮助文件。

TreeBagger的构造函数`B = TreeBagger(NTrees,X,Y,'param1',val1,'param2',val2,...)`，
其中有一个参数NVarToSample的说明如下，表明这个类可以用来做随机森林（Random Forest）。
>
NVarToSample	
Number of variables to select at random for each decision split. Default is the square root of the number of variables for classification and one third of the number of variables for regression. Valid values are 'all' or a positive integer. Setting this argument to any valid value but 'all' invokes Breiman's 'random forest' algorithm.

用法：

	B = TreeBagger(nTree,train_data,train_label);
	predict_label = predict(B,test_data，'NVarToSample','all');

参考matlab示例代码来编写测试代码：
	
	load fisheriris %载入fisheriris数据包
	load test_data %test_data需要自己弄，matlab不提供
	
	%建造50棵树，训练集meas，类别species,'NVarToSample','all'表示使用randmon forest算法
	b = TreeBagger(50,meas,species,'OOBPred','on','NVarToSample','all')  

	%画出out-of-bag的比率
	plot(oobError(b))
	xlabel('number of grown trees')
	ylabel('out-of-bag classification error')

	%预测
	[Y] = predict(b, test_data);	%这里也可以用meas这个训练数据集来做测试
Y内的数据即为最终结果的预测分类。

下图结果表明，随着tree个数的增多，out-of-bag率在下降：
{% img right /images/pic/random_trees_result.png 500 500  'sina' 'sina' %}
	
	
The end.

###参考资料
	
* [leftnoteasy](http://www.cnblogs.com/LeftNotEasy/archive/2011/03/07/1976562.html)
* [RandomForest wiki](http://zh.wikipedia.org/wiki/%E9%9A%8F%E6%9C%BA%E6%A3%AE%E6%9E%97)
* [Python实现的随机森林](http://www.oschina.net/translate/random-forests-in-python?cmp)
* [handspeaker](http://www.cnblogs.com/hrlnw/p/3850459.html)
