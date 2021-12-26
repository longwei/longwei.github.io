---
layout: post
title: How to Solve it
---


How to solve it?

面对未知问题时候，几个个常用办法是
1. 把位置问题转化为已知问题，
2. 从小而具体的case开始分析，找出规律
3. divide and conquer， 假设我们已经解决了一部分问题，so what?

如果能将问题切分为更小的问题，那么只要加上memorization，那就是一个DP解法。
其实backtracking, 枚举也是一种divide and conquer，只不过不是等分。

穷举并剪枝
===
这是最暴力也是最容易想到的办法，基本思路是可能的线性查找，在可能的解空间树内一个一个做dfs穷举。
如果发现这个leaf一定无解， 早点prune剪枝。

不变量invariant或者monovariant约束
===
通过构造一个invariant windows, 那么需要确定的事，当加入一个新的元素时， 如何maintain invariant window。
找出每次操作只有的关键变量，利用这个变量来解



DP不是算法，更像是divide & conquer的一种形式
===
将问题划分为更小规模的问题，然后利用小规模问题的解推导出大规模问题的解

1)  假设已知部分解法，将已知问题问题加一 “假如我已经知道了0…N范围内的所有解，我能不能利用它们获得N+1的解？
2） 将问题分成两半，然后合并两者结果 假如我已经知道了两个规模为N/2的问题的解，我能不能在O(N)时间内将它们组合成一个规模为N的解？


不要怕穷举，计算的本质就是穷举，先搞出来再考虑优化的可能性





