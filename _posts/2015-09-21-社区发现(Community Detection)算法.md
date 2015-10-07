---
layout: default
title: 社区发现(Community Detection)算法
comments: true
kind: PCI
---
本文主要转自csdn的文章：[Community Detection 算法 http://blog.csdn.net/itplus/article/details/9286905](http://blog.csdn.net/itplus/article/details/9286905)

##算法描述
![](http://img.blog.csdn.net/20130710080848421)

![](http://img.blog.csdn.net/20130710080858906)

从上述定义可以看出：社区是一个比较含糊的概念，只给出了一个定性的刻画。
另外需要注意的是，社区是一个子图，包含顶点和边。

![](http://img.blog.csdn.net/20130710080910046)

![](http://img.blog.csdn.net/20130710080925218)

##已有算法
![](http://img.blog.csdn.net/20130710081011421)

##LPA
![](http://img.blog.csdn.net/20130710081045968)
  标签传播算法（LPA）的做法比较简单：
  
第一步: 为所有节点指定一个唯一的标签；

第二步: 逐轮刷新所有节点的标签，直到达到收敛要求为止。对于每一轮刷新，节点标签刷新的规则如下:

对于某一个节点，考察其所有邻居节点的标签，并进行统计，将出现个数最多的那个标签赋给当前节点。当个数最多的标签不唯一时，随机选一个。

注：算法中的记号 N_n^k 表示节点 n 的邻居中标签为 k 的所有节点构成的集合。

![](http://img.blog.csdn.net/20130710081059265)

##SPLA
![](http://img.blog.csdn.net/20130710081109859)

![](http://img.blog.csdn.net/20130710081127656)


  SLPA 中引入了 Listener 和 Speaker 两个比较形象的概念，你可以这么来理解：在刷新节点标签的过程中，任意选取一个节点作为 listener，则其所有邻居节点就是它的 speaker 了，speaker 通常不止一个，一大群 speaker 在七嘴八舌时，listener 到底该听谁的呢？这时我们就需要制定一个规则。

  在 LPA 中，我们以出现次数最多的标签来做决断，其实这就是一种规则。只不过在 SLPA 框架里，规则的选取比较多罢了（可以由用户指定）。
 
  当然，与 LPA 相比，SLPA 最大的特点在于：它会记录每一个节点在刷新迭代过程中的历史标签序列（例如迭代 T 次，则每个节点将保存一个长度为 T 的序列，如上图所示），当迭代停止后，对每一个节点历史标签序列中各（互异）标签出现的频率做统计，按照某一给定的阀值过滤掉那些出现频率小的标签，剩下的即为该节点的标签（通常有多个）。

SLPA 后来被作者改名为 GANXiS，且软件包仍在不断更新中......

更多内容参考：[http://blog.csdn.net/itplus/article/details/9286905](http://blog.csdn.net/itplus/article/details/9286905)
