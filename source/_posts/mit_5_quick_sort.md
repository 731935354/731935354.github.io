---
title: MIT算法导论笔记(5):快速排序
tags:
  - 算法
categories: 技术
date: 2017-10-19 10:08:00
mathjax: true
---

快速排序算法由Tony Hoare在1962年发明。

<!--more-->

# 特点

* 用到了分治法
* 节省内存（不需要额外的存储空间）
* 很实用（需要将标准步骤微调一下）

# 步骤

1. 分：将原数组以**主元**分成两个子数组，第一个数组里的数都比主元小，第二个数组里的数都不小于主元
![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_partition.png)
2. 治：递归地对两个子数组排序
3. 合：常数时间

## 数组划分
### 伪代码
```
Partition(A, p, q) // A[p...q]
	x ← A[p]
	i ← p
	for j ← p+1 to q
		do if A[j] <= x
			then i ← i+1
				exch A[i],A[j]
	exch A[p],a[i]
	return i
```
p和q分别是数组左右下标的边界  
如伪代码第一行所示，我们选取A[p]为主元x。  
在程序运行过程中，我们想要保持以下循环不变量：  
**数组里比主元x小的数，都被移到了下标i以前，下标i+1到j-1中的的数据都大于或等于x**（j以后的数据我们还不知道）。

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_quick_sort_1.png)

在程序刚开始的时候，i从p开始，j从p+1开始，此刻只有主元x是已知的，在循环中，程序会检查下标j的元素，看它是否大于或等于x，如果确实如此，我们不需要做任何额外的工作，直接把j加1，然后循环不变量就满足了；如果这个数据小于或等于x，则需要额外工作来维持循环不变量，做法如下：将下标为i+1的元素与下标为j的元素互换，再将i加1，这样，循环不变量就又满足了。
$T(n)=\Theta(n)$

### 例子
过程解析：

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_quick_sort_2.png)

* 【图第1行】程序开始时，设定6为主元，i和j分别为第一个元素和第二个元素的下标
* 【图1-3行】运行过程中，j不断增加，直到遇到一个小于主元6的元素，将下标i+1的元素与下标j的元素互换，同时i加1
* 【图第4行】当j移出数组后，循环结束，做最后一次交换：交换主元与下标i元素
* 【图第5行】划分结束，主元不小于左侧元素，不大于右侧元素

# 快速排序伪代码
```
Quicksort(A, p, q)
	if p < q
	then r ← Partition(A, p, q)
		Quicksort(A, p, r-1)
		Quicksort(A, r+1, q)
```
调用方法：Quicksort(A, 1, n)  
边界：当A[p,q]为空或只有一个元素时，Quicksort(A,p,q)没有任何工作量  
优化思路 

1. 由于代码是【尾递归】的（一个函数中所有递归形式的调用都出现在函数的末尾），可以采用尾递归优化方案
2. 可以为较短的数组“定制”更高效的代码。例如，如果现在有一个算法可以高效地对长度为5的数组进行排序，那么可以在Quicksort函数体中检查当前p到q数组长度是否为5，如果为5，则调用该高效的算法。

# 运行时间分析
假设：所有元素都是不同的
## 最坏情况
数组已有序，或逆序：每个划分的其中一边都没有元素
$$\begin{aligned} 
T(n) & = T(0)+T(n-1)+\Theta(n) \\
   & = \Theta(1) + T(n-1) + \Theta(n) \\
   & = T(n-1) + \Theta(n) \\
   & = \Theta(n^2) (等差级数，和插入排序类似)
\end{aligned}$$
### 递归树
$T(n)=T(0)+T(n-1)+cn$  
逐层展开

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_recursive_tree_1.png)

最终结果

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_recursive_tree_2.png)

* 将所有子树的顶点相加
$\Theta(\sum_{k=1}^{n}ck)=\Theta(n^2)$  
* 将左分支的所有$T(0)$相加，每一项为$\Theta(1)$，共有n项，所以和为$\Theta(n)$
* 综上$T(n)=\Theta(n)+\Theta(n^2)=\Theta(n^2)$

## 最优情况(仅用于直观理解)
* 如果足够幸运的话，每次划分使得两个子数组长度相同  
$T(n)=2T(\frac{n}{2})+\Theta(n)=\Theta(n\lg{n})$

* 如果每次划分使得两个子数组长度为1:9  
$T(n)=T(\frac{n}{10})+T(\frac{9n}{10})+\Theta(n)$
$【\Theta(n) \leqslant cn】$  
递归树  
逐层展开    

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_recursive_tree_3.png)

最终结果

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_5_recursive_tree_4.png)

观察递归树，最左侧的路径长度为$\log_{10}{n}$，最右侧路径长度为$log_{\frac{9}{10}}{n}$，其余路径长度介于这两者之间    
每层计算成本如下 

| 层数 | 计算成本 |
| :------ |:------|
| 1 | $cn$ |
| 2 | $cn$ |
| 3 | $cn$ |
| $\dots$ | $\dots$ |
| $\log_{10}{n}$ | $cn$ |
| $\log_{10}{n}+1$ | $\leqslant cn$ |
| $\dots$ | $\dots$ |
| $log_{\frac{9}{10}}{n}$ | $\leqslant cn$ |

综上  
$cn\log_{10}{n}+\Theta(n) \leqslant T(n) \leqslant cn\log_{\frac{9}{10}}{n}+\Theta(n)$  
$T(n)=\Theta(n\lg{n})$

## 交替情况
假设在划分数组的时候，“幸运”和“不幸运”的情况交替发生  

* 幸运: $L(n)=2U(n/2)+\Theta(n)$  
* 不幸运: $U(n)=L(n-1)+\Theta(n)$   

则
$$\begin{aligned}
L(n) & = 2(L(\frac{n}{2}-1)+\Theta(\frac{n}{2}))+\Theta(n) \\
     & = 2L(\frac{n}{2}-1)+\Theta(n) \\
     & = \Theta(n\lg{n})
\end{aligned}$$
可见，在这种情况下，运行时间仍是“幸运”的

## 随机化快速排序 
如何保证运行时间总是“幸运”的？

* 方案1：先对数组进行随机排序  
* 方案2：随机选择主元

### 优点
* 运行时间不依赖于输入序列的顺序
* 无需对输入序列的分布做任何假设
* 没有一种输入会引起最差的运行效率
* 最差的情况仅由随机数产生器决定

### 运行时间分析
此处选择方案2：随机选择主元  
具体做法：在划分数组之前，先将数组第一个元素与任意元素互换（包括本身），然后再按之前的流程划分数组
$T(n)=算法运行时间的随机变量（假设随机数是独立的)$  
对于$k=0, 1, \cdots, n-1$，令  
$$
X_k = 
\begin{cases}
1 \quad 如果划分长度比为k:n-k-1\\
0 
\end{cases}
$$
这种随机变量通常称作“随机指示变量”

$$\begin{aligned}
E[X_k] & = 0 \cdot Pr\{X_k=0\} + 1 \cdot Pr\{X_k=1\} \\ 
 & = 1 \cdot Pr\{X_k=1\} \\ 
 & = \frac{1}{n}
\end{aligned}$$

由
$$\begin{aligned}
T(n) & = 
\begin{cases}
T(0) + T(n-1) + \Theta(n) \quad 如果划分比例为0:n-1 \\
T(1) + T(n-1) + \Theta(n) \quad 如果划分比例为1:n-2 \\
\cdots \\
T(n-1) + T(0) + \Theta(n) \quad 如果划分比例为n-1:0 \\
\end{cases} \\
& = \sum_{k=0}^{n-1}{X_k(T(k)+T(n-k-1)+\Theta(n))}
\end{aligned}
$$
得出
$$\begin{aligned}
E[T(n)] & = E[\sum_{k=0}^{n-1}{X_k(T(k)+T(n-k-1)+\Theta(n))}]\\
 	    & = \sum_{k=0}^{n-1}E[X_k(T(k)+T(n-k-1)+\Theta(n)]【期望的线性性质】\\
 	    & = \sum_{k=0}^{n-1}E[X_k] \cdot E[T(k)+T(n-k-1)+\Theta(n)]【X_k独立于T(k),T(n-k-1),\Theta(n)】\\
 	    & = \frac{1}{n}\sum_{k=0}^{n-1}E[T(k)] + \frac{1}{n}\sum_{k=0}^{n-1}E[T(n-k-1)] + \frac{1}{n}\sum_{k=0}^{n-1}\Theta(n)【期望的线性性质】\\
 	    & = \frac{2}{n}\sum_{k=0}^{n-1}E[T(k)] + \Theta(n) \\
\end{aligned}$$

将$k=0,1$的项吸收到$\Theta(n)$中来，可以使计算简便。则
$$E[T(n)] = \frac{2}{n}\sum_{k=2}^{n-1}E[T(k)] + \Theta(n)$$

#### 证明$E[T(n)] \leqslant an\lg{n}$，a为大于0的常数
* 基本情况：选择一个足够大的a，使得当n较小时，$an\lg{n} \geqslant E[T(n)]$【只有当$n=0,1$的情况排除时才成立】

* 代换法：  
利用不等式
$\sum_{k=2}^{n-1}{k\lg{k}} \leqslant \frac{1}{2}n^2\lg{n}-\frac{1}{8}n^2$【[证明](#jump)】  
$$\begin{aligned}
E[T(n)] & \leqslant \frac{2}{n}\sum_{k=2}^{n-1}{ak\lg{k}} + \Theta(n) \\
 & \leqslant \frac{2a}{n}(\frac{1}{2}n^2\lg{n}-\frac{1}{8}n^2) + \Theta(n) \\
 & = an\lg{n} - (\frac{an}{4}-\Theta(n)) \\
 & \leqslant an\lg{n} \quad if \quad \frac{an}{4} \geq \Theta(n) \\
\end{aligned}$$

在实践中，随机化快速排序通常比归并排序快3倍以上，而且它在虚拟内存的缓存中性能很好。

# 附录 
证明:$\sum_{k=2}^{n-1}{k\lg{k}} \leqslant \frac{1}{2}n^2\lg{n}-\frac{1}{8}n^2$
<span id="jump"></span>

* 利用和的性质
	+ 待完成
* 利用求和的积分方法  
	+ $\sum_{k=2}^{n-1}{k\lg{k}} = \sum_{k=1}^{n-1}{k\lg{k}}$
	+ 当$x \geqslant 2$时，$(x\lg{x})' = \lg{x} + x \cdot \frac{1}{x\ln{2}} = \lg{x} + \frac{1}{\ln{2}} > 0$
	+ 所以有
$$\begin{aligned}
\sum_{k=2}^{n-1}{k\lg{k}} & = \sum_{k=1}^{n-1}{k\lg{k}} \\
 & \leqslant \int_0^n{x\lg{x}dx} \\
 & = \left. (\frac{1}{2}x^2\lg{x}-\frac{x^2}{4\ln{2}}) \right| _{0}^{n} \\
 & = \frac{1}{2}n^2\lg{n}-\frac{n^2}{4\ln{2}} \\ 
 & \leqslant \frac{1}{2}n^2\lg{n}-\frac{1}{8}n^2 \\
\end{aligned}$$


