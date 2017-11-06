---
title: MIT算法导论笔记(7):顺序统计、中值
tags:
  - 算法
categories: 技术
date: 2017-11-06 12:50:00
mathjax: true
---

# 顺序统计
给定包含n个数的无序数组A，找到第k小的元素

* $k=1$，最小值
* $k=n$，最大值
* $k=\lfloor \frac{n+1}{2} \rfloor 或 \lceil \frac{n+1}{2} \rceil$，中位数

朴素算法：对A排序，返回A[k]。如果采用堆排序或归并排序，则运行时间为$O(n\lg{n})$

<!--more--> 

## 随机分治算法
### 随机选择
```
Rand-Select(A, p, q, i) // 寻找A[p...q]中第i小的元素
	if p = q then return A[p]
		// 在A[p...q]中随机选择一个元素x，以x将A[p...q]划分成左右两部分，
		左侧元素小于或等于x，右侧元素大于或等于x，x所在下标为r
		r ← Rand-Partition(A, p, q) 
		k ← r - p + 1 // A[r]是A[p...q]中第k小的元素
		if i = k then return A[r]
		if i < k then
			return Rand-Select(A, p, r-1, i)
		else 
			return Rand-Select(A, r+1, q, k-i)
```
### 举例
找出数组A=[6, 10, 13, 5, 8, 3, 2, 12]中第7小的元素

![随机选择](http://owruh8822.bkt.clouddn.com/mit_7_random_select.png)

* 选取6为主元，对A数组进行划分
* 划分结果如图第二行。r=4
* 由于4 < 7，所以在右侧子数组中（绿色部分）递归查找第（7-4=3）小的元素
* 以此类推，直到找到A中第k小元素

### 运行时间分析
#### 直观理解
前提：假设所有元素都不相等（无重复元素）  
##### 幸运的情况
划分时，左右两侧元素数量相等（事实上，只要左右两侧数组长度比例为常数，算法的期望运行时间都与在正中央划分的情况一样好  
以两数组长度比1：9为例
$$T(n) \leqslant T(\frac{9}{10}n)+\Theta(n)$$
根据主定理，$n^{\log_{b}{a}}=n^{\log_{\frac{9}{10}}{1}}=n^0=1$多项式地小于$n$，属于情况3，所以
$$T(n)=\Theta(n)$$  
##### 不幸运的情况
划分长度比例为0：n-1  
$$
\begin{aligned}
T(n) & = T(n-1)+\Theta(n) \\
	 & = \Theta(n^2) (等差级数)\\
\end{aligned}$$
#### 期望运行时间

* 用$T(n)$表示输入规模为n时算法运行时间的随机变量，假设随机选择算法的调用是相互独立的。
* 定义指示器随机变量$X_k$，$k=0, 1, \cdots, n-1$
$$X_k=
\begin{cases}
1 (划分长度比为k：n-k-1) \\
0 \\
\end{cases}$$

则运行时间如下：
$$
\begin{aligned}
	T(n) 
	& \leqslant 
	\begin{cases}
		T(max\{0, n-1\})+\Theta(n) \quad (0:n-1) \\
		T(max\{1, n-2\})+\Theta(n) \quad (1:n-2) \\
		\cdots \\
		T(max\{n-1, 1\})+\Theta(n) \quad (n-1:0) \\
	\end{cases} \\
	& = \sum_{k=0}^{n-1}X_k(T(max\{k, n-k-1\})+\Theta(n))
\end{aligned}$$

期望运行时间
$$\begin{aligned}
E[T(n)]
& = E[\sum_{k=0}^{n-1}X_k(T(max\{k, n-k-1\})+\Theta(n))] \\
& = \sum_{k=0}^{n-1}E[X_k(T(max\{k, n-k-1\})+\Theta(n))] \quad 【期望的线性性质】\\
& 【求和式中所有的X_k的值都取决于“顶层”的随机数产生器，T(max\{k, n-k-1\}) \\ 
& 取决于“递归”的随机数产生器，二者不在同一个层次，所以是相互独立的。】 \\
& = \sum_{k=0}^{n-1}E[X_k] \cdot E[T(max\{k, n-k-1\})+\Theta(n)] \quad 【X和Y相互独立时，E[XY]=E[X]E[Y]】\\
& = \frac{1}{n}\sum_{k=0}^{n-1}E[T(max\{k, n-k-1\})] + \frac{1}{n}\sum_{k=0}^{n-1}\Theta(n) \\
& 【求和式中相同的式子出现了两次，如k=0和k=n-1时，T(max\{k, n-k-1\})都为T(n-1) \\
& k=1和k=n-2时，T(max\{k, n-k-1\})都为T(n-2)，因此以\lfloor \frac{n}{2} \rfloor为界 \\
& 并提取公因式（当n为奇数时，正中央的元素计算了两次，所以是期望运行时间的上界如下。】 \\
& \leqslant \frac{2}{n}\sum_{k = \lfloor \frac{n}{2} \rfloor}^{n-1}E[T(k)] + \Theta(n) \\
\end{aligned}$$

##### 证明期望运行时间为线性复杂度
当遇到复杂的递归式，并且已经大概猜想出算法复杂度上界，通常用代换法证明。  
思路：证明存在常数$c > 0$，使得对任意的$n$，都有$E[T(n)] \leqslant c \cdot n$  
代换法  
假设：存在常数$c > 0$，当$r < n$时，都有$E[T(r)] \leqslant c \cdot r$  
当r=n时  
$$\begin{aligned}
E[T(r)] 
& \leqslant \frac{2}{r}\sum_{k = \lfloor \frac{r}{2} \rfloor}^{r-1}E[T(k)] + \Theta(r) \\
& \leqslant \frac{2}{r}\sum_{k = \lfloor \frac{r}{2} \rfloor}^{r-1}c \cdot k + \Theta(r) \\ 
& = \frac{2c}{r}\sum_{k = \lfloor \frac{r}{2} \rfloor}^{r-1}k + \Theta(r) \\
& \leqslant c \cdot r - (\frac{1}{4}cr - \Theta(r)) 【\sum_{k = \lfloor \frac{n}{2} \rfloor}^{n-1}k \leqslant \frac{3}{8}n^2】\\
& \leqslant c \cdot r \quad 【如果\frac{1}{4}cr - \Theta(r) > 0】 \\ 
& \Theta(r)是一个定值，因此总存在足够大的c，使得\frac{1}{4}cr - \Theta(r) > 0 \\
\end{aligned}$$
综上，得证。

随机选择算法的期望运行时间为$\Theta(n)$，最坏情况运行时间为$\Theta(n^2)$。

## 最坏情况为线性复杂度的顺序统计
思路：递归地选择一个好的主元  
考虑到递归算法的运行时间，如果将原问题拆分成两个子问题，则运行时间为$T(n)=2T(\frac{n}{2})+\Theta(n)=\Theta(n\lg{n})$(类似于归并排序)。为了保证顺序统计算法最坏情况的时间复杂度为线性的，我们需要一个时间复杂度严格小于$\Theta(n)$的递归算法。这也是下述算法的关键。
```
Select(i, n) // 找到长度为n的数组中第i小的元素
	1. 将数组拆分成floor(n/5)组，每组5个元素（最后一组可以少于5个元素），
	并找到每组的中位数【时间复杂度：θ(n)】
	2. 递归地找出floor(n/5)个中位数的中位数x【时间复杂度：T(n/5)】
	3. 以x为主元划分数组，令k=rank(x)
	4. if i = k，return x
	   if i < k, 在包含较小数值的部分递归寻找第i小的元素
	       else, 在包含较大数值的部分递归寻找第i-k小的元素
```

![选择算法](http://owruh8822.bkt.clouddn.com/mit_7_select_1.png)

* 有一个方框的元素为其所在组的中位数
* 有两个方框的元素为所有组的中位数的中位数

### 线性复杂度证明
#### 直观理解
到目前为止，我们还不能写出算法的递归式，因为子问题的规模是未知的。  
子问题的规模是数组划分之后的上半部分或下半部分。  
如果很不幸得到0:n-1的划分，子问题的规模将为n-1，递归的总体复杂度为$\Theta(n^2)$。  
所以这个算法要足够好，保证可以得到“好的划分”。  
具体来说，算法的第3、4步与之前的【随机选择】算法是完全相同的。这两步的时间复杂度应为$T(cn)$，考虑到算法的第2步时间复杂度为$T(\frac{n}{5})$，如果c严格小于$\frac{4}{5}$，则算法以常数因子让问题规模减小。这种情况下把所有的子问题加在一起，则会得到一个严格小于n的常数，这保证了算法是几何的，如果它是几何的，将可以得到线性时间算法。

#### 求解c
说明：从节点a指向节点b的箭头，表示$a<b$

![大小关系](http://owruh8822.bkt.clouddn.com/mit_7_select_2.png)

由图可知

* 至少有3$\lfloor \lfloor \frac{n}{5} \rfloor / 2 \rfloor$个元素小于或等于x
* 同样，至少有3$\lfloor \lfloor \frac{n}{5} \rfloor / 2 \rfloor$个元素大于或等于x

进一步

* 至少有$\lfloor \lfloor \frac{n}{5} \rfloor / 2 \rfloor$个组内中位数小于或等于x
* 已知：$\lfloor \lfloor \frac{n}{5} \rfloor / 2 \rfloor = \lfloor \frac{n}{10} \rfloor$
* 因此，有3$\lfloor \frac{n}{10} \rfloor$个元素小于或等于x，大约有$\frac{3}{10}n$

简化

* 如果$n>50$，3$\lfloor \frac{n}{10} \rfloor > \frac{n}{4}$

因此，我们可知，如果数组元素个数大于50，对数组划分之后，较短的部分元素个数大于$\frac{n}{4}$，另一部分元素个数小于$\frac{3n}{4}$。  
$\frac{3}{4}$严格小于$\frac{4}{5}$。

#### 运行时间分析
$T(n) \leqslant T(\frac{n}{5}) + T(\frac{3n}{4}) + \Theta(n)$

假设：$T(n) \leqslant cn$  
证明：代换法
$$\begin{aligned}
T(n) & \leqslant \frac{cn}{5} + \frac{3}{4}cn + \Theta(n) \\
     & = \frac{19}{20}cn + \Theta(n) \\
     & = cn - (\frac{1}{20}cn - \Theta(n)) \\
     & \leqslant cn \quad 当c足够大时成立 \\
\end{aligned}$$

# 附录
## 证明$\sum_{k = \lfloor \frac{n}{2} \rfloor}^{n-1}k \leqslant \frac{3}{8}n^2$
数学归纳法：

* 当n=2时，$1 \leqslant \frac{3}{2}$成立
* 假设：当$n = t(t \geqslant 2)$时，$\sum_{k = \lfloor \frac{t}{2} \rfloor}^{t-1}k \leqslant \frac{3}{8}t^2$成立
* 当n=t+1时

$$\begin{aligned}
\sum_{k = \lfloor \frac{t+1}{2} \rfloor}^{t}k & \leqslant \frac{3}{8}t^2 + t\\
& \leqslant \frac{3}{8}t^2 + \frac{3}{4}t + \frac{3}{8} \\
& = \frac{3}{8}(t+1)^2 \\ 
\end{aligned}$$

综上，得证

## 为什么要将5个元素分成一组？
### 将3个元素分成一组 
```
Select(i, n) // 找到长度为n的数组中第i小的元素
	1. 将数组拆分成floor(n/3)组，每组3个元素（最后一组可以少于3个元素），
	并找到每组的中位数【时间复杂度：θ(n)】
	2. 递归地找出floor(n/3)个中位数的中位数x【时间复杂度：T(n/3)】
	3. 以x为主元划分数组，令k=rank(x)【时间复杂度：θ(n)】
	4. if i = k，return x
	   if i < k, 在包含较小数值的部分递归寻找第i小的元素
	       else, 在包含较大数值的部分递归寻找第i-k小的元素
```
假设第4步的时间复杂度为$T(cn)$  
则算法整体复杂度为$T(n)=T(\frac{n}{3})+T(cn)+\Theta(n)$  
只有当c严格小于$\frac{2}{3}$时，才能得到线性复杂度。即对数组划分后，包含较多元素的部分，其元素个数要严格小于$\frac{2n}{3}$。  
根据算法的第1步和第2步，可知

* 至少有$2 \lfloor \lfloor \frac{n}{3} \rfloor / 2 \rfloor$个元素小于x
* 同样，至少有$2 \lfloor \lfloor \frac{n}{3} \rfloor / 2 \rfloor$个元素大于x

只有$\frac{1}{3}n < 2 \lfloor \lfloor \frac{n}{3} \rfloor / 2 \rfloor < \frac{2}{3}n$成立时，c严格小于$\frac{2}{3}$

考虑到当$n=3k(k=0,1,2\cdots)$时，$\frac{1}{3}n = 2 \lfloor \lfloor \frac{n}{3} \rfloor / 2 \rfloor = 2\lfloor \frac{n}{6} \rfloor$，上述不等式不成立，因此无法保证算法整体复杂度为线性。

### 保证线性复杂度的最小整数
由上述分析可知  
假设将数组以k个元素分为一组，假设算法第4步时间复杂度为$T(cn)$  
总体时间复杂度：$T(n)=T(\frac{n}{k})+T(cn)+\Theta(n)$  

![](http://owruh8822.bkt.clouddn.com/mit_7_select_3.png)

* 至少有$\lceil \frac{k}{2} \rceil \lfloor \lfloor \frac{n}{k} \rfloor / 2 \rfloor$个元素小于x
* 同样，至少有$\lceil \frac{k}{2} \rceil \lfloor \lfloor \frac{n}{k} \rfloor / 2 \rfloor$个元素大于x

我们需要找到当n足够大时，使以下不等式成立的最小的正整数k
$$\frac{1}{k}n < \lceil \frac{k}{2} \rceil \lfloor \lfloor \frac{n}{k} \rfloor / 2 \rfloor < \frac{k-1}{k}n$$
其等价于
$$\frac{1}{k}n < \lceil \frac{k}{2} \rceil \lfloor \frac{n}{2k} \rfloor < \frac{k-1}{k}n$$
综上可知，5是保证选择算法线性复杂度的最小整数。  
以更多元素为一组也是可行的，但问题规模随递归减小的速度会变慢，性能提升并不明显。每组中元素个数越多，算法越接近指数复杂度。