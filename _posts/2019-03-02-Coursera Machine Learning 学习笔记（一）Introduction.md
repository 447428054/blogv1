---
layout:     post
title:      Coursera Machine Learning 学习笔记（一）Introduction
subtitle:   Introduction
date:       2019-03-02
author:     JMX
header-img: img/post_bd_02.jpg
catalog: true
tags:
    - 机器学习
    - Introduction
---

> 文章为博主学习Coursera上的Machine Learning课程的笔记，来记录自己的学习过程，欢迎大家一起学习交流

# 01:Introduction

## 机器学习的定义
- Arthur Samuel(1959)
	**Machine Learning:**"Field of study that gives computers the ability to learn without being explicitly programmed"

- Tom Michel(1999)
	**Well posed learning problem:**"A computer program is said to learn from experience E with respect to some class of tasks T and performance measure P, if its performance at tasks in T, as measured by P, improves with experience E."
	在下棋的例子中：
1. E经验为1000场下棋游戏
2. T任务为下棋
3. P评价准则为是否获胜

- 学习算法的类型
	。 有监督学习（Supervised learning）
			Teach the computer how to do something, then let it use it;s new found knowledge to do it
			注：一般是有标注的训练集
	。无监督学习（Unsupervised learning）
			Let the computer learn how to do something, and use this to determine structure and patterns in data
			注：一般是无标注训练集，使机器从中提取特征，常见算法为聚类
	。强化学习（Reinforcement learning）
	。推荐系统（Recommender systems）

### 有监督学习介绍

##### 问题分类：
- 预测问题
课程内拿房价预测作为示例：具体可以看课程内容
预测问题也叫回归问题，具有以下特征：
1. 预测连续的输出
2. 没有明显得离散划分

- 分类问题
课程内以肿瘤划分作为示例

### 无监督学习介绍

在无监督学习里我们获得的是没有标注的数据，将这些数据划分成不同的数据簇

##### 聚类算法

具体应用示例：
1. 新闻划分
2. 基因组排序
3. 分布式计算机集群划分
4. 社交网络划分
5. 天文数据分析
