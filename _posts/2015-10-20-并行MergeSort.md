---
layout: default
title: 并行MergeSort（并行排序算法）
comments: true
kind: algorithm
---
本文章参考自[Rachel-Zhang's blog](http://blog.csdn.net/abcjennifer/article/details/47110991)，感谢作者。

###计算量分布
数据结构课上我们都学过经典归并排序： 基于divide & conquer 思想， 每次将一个array分拆成两部分， 分别排序后合并两个有序序列。 可以通过 T(n)=2T(n/2)+n 得到， 其complexity = O(nlogn)。 和I.1节类似， 我们看看merge sort中的哪些步是可以并行的。

这里可以将基于merge sort的大规模数据排序分为三部分。 经过divide步之后， 数据分布如图所示： 

![](http://img.blog.csdn.net/20150910095654173)

最下端的为 *大量-短序列* 的合并； 
中间一块为*中等数量-中等长度序列* 的合并； 
最上端的为*少量-长序列* 的合并；

我们分这三部分进行并行化。 之后大家会明白为啥要这么分~

###II.1 Step 1: Huge number of small tasks
这一部分，每一部分序列合并的代价很小， 而有众多这样的任务。 所以我们采取给每个merge一个thread去执行， thread 内部就用串行merge的方法。 

###II.2 Step 2: Mid number of mid task
在这一阶段， 有中等数量的task， 每个task的工作量也有一定增长。 所以我们采取给每个merge一个SM去执行， 每个block上运行的多个thread并行merge的方法。 和step1中的主要区别就是block内部merge从串行改成了并行。 那么怎样去做呢？

如下图所示，假如现在有两个长为4个元素的array， 想对其进行排序， 将merge排序结果index 0 - 7 写入数据下方方块。

<center>![](http://img.blog.csdn.net/20150910095613621)</center>

做法： 
对于array中的每个数字， 看两个位置： 
  1. 自己所在序列的位置： 看它前面有几个元素 
  2. 另一个序列的位置： 另一个序列中有多少个元素比它小（采用二分搜索）

这样做来， 第一步O(1)可得到， 第二步二分查找O（logn）可得到； 整个merge过程用shared memory存结果。

###II.3 Step 3: Small number of huge task
第三个环节中，也就是merge的顶端（最后一部分），每个merge任务的元素很多， 但是merge任务数很少。 这种情况下， 如果采用Step2的方法， 最坏的情况就是只有一个很大的task在跑， 此时只有1个SM在忙， 其他空闲SM却没法利用， 所以这里我们尝试将一个任务分给多个SM执行。

方法： 如图2.3所示， 将每个序列以256个元素为单位分段， 得到两个待merge序列In1和In2。然后，对这些端节点排序，如EABFCGDH。 如step2中的方法， 我们计算每个段节点在另一个短序列（长256）中的位置， 然后只对中间那些不确定的部分进行merge排序， 每个merge分配一个SM。

如E~A的部分， 
  1. 计算出E在In1中的位置posE1, A在In2中的位置posA2 
  2. merge In1中 posE1~A 和 In2中 E~posA2的元素

<center>![](http://img.blog.csdn.net/20150910095750296)</center>

###II.4 Merge sort in GPU
GPU代码请参考原文：[http://blog.csdn.net/abcjennifer/article/details/47110991](http://blog.csdn.net/abcjennifer/article/details/47110991)

