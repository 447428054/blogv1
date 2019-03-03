---
layout:     post
title:      Coursera Machine Learning 学习笔记（二）Linear Regression
subtitle:   Linear Regression
date:       2019-03-02
author:     JMX
header-img: img/post_bd_02.jpg
catalog: true
tags:
    - 机器学习
    - Linear Regression
---

> 文章为博主学习Coursera上的Machine Learning课程的笔记，来记录自己的学习过程，欢迎大家一起学习交流


# 02：Linear Regression

仍然以房价预测作为示例，具体示例仍需见课程内容。
符号含义：
1. m 为数据集的大小
2. x's为输入数据
3. y's为对应的目标输出结果
4. (x,y)为所有训练数据
5. (x<sup>i</sup>, y<sup>i</sup>)为具体第i行数据，第i个训练数据

假设函数h(x)，以一元线性回归为例：
![假设函数](https://img-blog.csdnimg.cn/20190302112701595.png)
$$\theta_0：截距  \theta_1：梯度$$

##### Linear regression - implementation（损失函数cost function）
计算由不同θ 取值带来的不同损失函数值，本质上是一个最小化问题：使下式取值最小
$$Minimize ：(h_\theta(x)-y)^2 $$
即可以看成下述式子：
$$J(\theta_0,\theta_1) = \frac 1 {2m}\sum_1^m(h_\theta(x^{(i)})-y^{(i)})^2$$

$$\frac1 m 是求平均\frac1 {2m}是为了数学计算方便$$
这个损失函数是均方误差，适用于多类回归问题，当然也可以有其他的损失函数。

##### 梯度下降算法（Gradient descent algorithm）
**目的：** 使损失函数J最小

###### 工作方式
- 从随机初始化开始
1. 对θ 随机赋值，可以为任何值
2. 每次一点点改变θ，使J(θ)减小
- 每次沿着梯度下降最大的方向
- 重复上述操作直到达到局部最小值
- 从哪里开始的可能决定你到达哪个局部最优解
![梯度下降](https://img-blog.csdnimg.cn/2019030217133153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjc2MDMz,size_16,color_FFFFFF,t_70)
**一个更正规的定义：**
做下述操作直到收敛：
![参数更新](https://img-blog.csdnimg.cn/20190302171951356.png)
符号解释：
1. **:=** 
表示赋值
NB a = b 是一个正确的断言
2. α (alpha)
学习率，控制参数更新的步长
   1. 如果学习率过大，可能无法得到最优解，误差会增大
   2. 如果学习率过小，那么达到最优解需要非常多步，耗费很长时间

注： 参数的更新必须**同步**即需要一个中间变量保存之前的值，原因是二者的式子中包含了对方，一方的更新会导致第二方式子内值的变化。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190302173355106.png)
**当我们达到局部最优解时：**
1. 后面部分的梯度为0
2. 各参数值就保持不变了 

##### 使用梯度下降的线性回归算法

- 将梯度下降算法应用到最小化损失函数J(θ)上

$$\frac{\partial} {\partial\theta_j}=\frac 1 {2m}\sum_1^m(h_\theta(x^{(i)})-y^{(i)})^2=\frac{\partial} {\partial\theta_j}\frac 1 {2m}\sum_1^m(\theta_0+\theta_1x^{(i)}-y^{(i)})^2$$
按照求导公式可以推出：
$$j=0:\frac{\partial} {\partial\theta_0}J(\theta_0,\theta_1)=\frac1 m\sum_1^m(h_\theta(x^{(i)})-y^{(i)})$$
$$j=1:\frac{\partial} {\partial\theta_1}J(\theta_0,\theta_1)=\frac1 m\sum_1^m(h_\theta(x^{(i)})-y^{(i)})*x^{(i)}$$

注：因为线性回归是一个凸函数，是一个碗形的图，所以会趋于局部最优解

- 现在的这种梯度下降算法又叫Batch Gradient Descent 原因是每一次都遍历了整个数据集，后面会提到取数据集中的部分进行的Gradient Descent
- 线性回归也有正规方程求解，但其中矩阵运算当数据集过大时不宜使用，这时就可以使用梯度下降

##### 数值求解的正规方程法
- 直接通过数值求解来避免繁琐的迭代过程，从数学上求解出min(J(θ))
**正规方程的优缺点：**
**优点：**
1.不需要学习率这个参数
2.对某些问题可以很快的解决
**缺点：**
会很复杂

##### 面对数据量很大的时候
这个时候就需要**将数据向量化**利用线性代数中矩阵运算来完成计算
