---
title: MIT算法导论笔记(3):求解递归式
tags:
  - 算法
categories: 技术
date: 2017-09-24 18:56:33
mathjax: true 
---
至今为止，求解递归式还没有一种通用的方法。  
本文介绍求解递归式的三个主要方法：代换法，递归树法，主方法。
<!--more-->

# 代换法
1. 猜出解的形式($n^2$/$n^3 \dots$)
2. 通过数学归纳法验证
3. 求解常数系数

例：$T(n)=4T(\frac{n}{2})+n$ $\quad$ $[T(1)=\Theta(1)]$  
**观察**  
由$T(n)=4T(\frac{n}{2})$可知(忽略$n$)，当$n$翻倍时，$T(n)$会变为原来的4倍。  
**猜想**  
$\Theta(n^2)$（直接证明$\Theta(n^2)$很难，先证明$\Theta(n^3)$）

## 证明$T(n)=O(n^3)$
* 猜想：$T(n)=O(n^3)$ 
* 假设：$k < n$时，$T(k) \leqslant ck^3$  
**注意：**代换法中不能使用$O$符号，需要用常数$c$来展开
	+ 原因：如果有一个有限项的大$O$关系序列，例如$n^2=O(n^3)=O(n^4)=O(n^4)$，由传递性可知$n^2=O(n^4)$，但如果大$O$关系序列是无限的，那么第一项不一定等于最后一项
	+ 例：假如想证明$n=O(1)$(这显然是不对的，否则所有算法都将是常数复杂度)，但却可以通过数学归纳法证明，如下：
		+ $1=O(1)$
		+ 假设$n-1=O(1)$
		+ 由于$n=(n-1)+1$，由假设，$n-1=O(1)$，且已知$1=O(1)$，所以总体是$O(1)$
		
	+ 错误原因：证明过程中$O(1)$所代表的常数是变化的，每次$O(1)$与$O(1)$相加，都可能把$O(1)$加倍，如果是常数次的加倍，那么最终结果仍然是$O(1)$，但如果是n次加倍，结果将从$O(1)$变为$O(n)$
	+ 结论：不能在$O$符号上进行归纳，代换法中需要用常数$c$来展开(保证不会在归纳过程中发生变化) 
* 推导
$$\begin{aligned} 
T(n) & =4T(\frac{n}{2})+n \\
   & \leqslant 4c(\frac{n}{2})^3+n\\
   & = \frac{1}{2}cn^3+n \\
   & =cn^3-(\frac{1}{2}cn^3-n) \\
   & \leqslant cn^3 \quad (if \frac{1}{2}cn^3-n \geqslant 0) \\
   & 当c \geqslant 1, n \geqslant 1 时不等式成立
\end{aligned}$$
* 最基本的情况：$T(1)=\Theta(1) \leqslant c * 1^3$，当$c \geqslant T(1)时成立$
* 可知，$T(n)=O(n^3)$，但这并不是一个严格的上界，严格的上界还有可能是$O(n^2)$。

## 证明$T(n)=O(n^2)$
* 猜想：$T(n)=O(n^2)$ 
* 假设：$k < n$时，$T(k) \leqslant ck^2$
* 推导(仿照上面的过程)
$$\begin{aligned} 
T(n) & =4T(\frac{n}{2})+n \\
   & \leqslant 4c(\frac{n}{2})^2+n\\
   & = cn^2+n \\
   & =cn^2-(-n) \\
   & \leqslant cn^2 \quad (if (-n) \geqslant 0) \\
   & 想要让(-n)非负是很难的
\end{aligned}$$

## 改进归纳假设
* 假设当$k<n$时$T(n) \leqslant c_1k^2-c_2k$ 
* 推导
$$\begin{aligned} 
T(n) & =4T(\frac{n}{2})+n \\
   & \leqslant 4(c_1(\frac{n}{2})^2-c_2(\frac{n}{2}))+n\\
   & = c_1n^2+(1-2c_2)n \\
   & =c_1n^2-c_2n-(-1+c_2)n \\
   & \leqslant c_1n^2-c_2n \quad (if (c_2-1) \geqslant 0) \\
\end{aligned}$$
* 最基本的情况：$T(1) \leqslant c_1 - c_2$，由于$T(1)=\Theta(1)$，是一个常数，因此需要$c_1 \geqslant T(1)+c_2$

# 递归树法
例：$T(n)=T(\frac{n}{4})+T(\frac{n}{2})+n^2$
将递归树展开如下  
![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_3_recursive_tree_1.png)  
展开到最后一步，所有的叶子节点都为$\Theta(1)$，如下图
![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_3_recursive_tree_2.png)  
**问题1：**总共有多少个叶子节点？  
观察递归展开的第一步，$T(n)=T(\frac{n}{4})+T(\frac{n}{2})$，由规模$n$变为规模$\frac{3}{4}n$，减少了$\frac{1}{4}$，
可以推测，所有的叶子节点数目加起来不超过$n$。  
**问题2：**总体的时间复杂度是多少？  
第一层：$n^2$  
第二层：$(\frac{n}{4})^2+(\frac{n}{2})^2=\frac{5}{16}n^2$  
第三层：$(\frac{n}{16})^2+(\frac{n}{8})^2+(\frac{n}{8})^2+(\frac{n}{4})^2=\frac{25}{256}n^2$  
发现规律：常数因子为**等比级数**。  
按层累加  
$$\begin{aligned} 
T(n) & < (1+\frac{5}{16}+\frac{25}{256}+\dots+(\frac{5}{16})^k+\dots)n^2 \\
   & < (1+\frac{1}{2}+\frac{1}{4}+\dots)n^2 \\
   & = 2n^2 \\
   & = O(n^2)
\end{aligned}$$  
可以看出，递归树法不太严谨，因为并没有严格证明每一层的系数构成等比级数，而且中间省略了很多计算步骤。通常的做法是，在用递归树法求解之后，
再用代换法证明。

#主方法
本质上是递归树方法的一个应用，但是它更精确。它基于**主定理**。  
缺点：只能应用到特定形式的递归式上，限制如下  

* $T(n)=aT(\frac{n}{b})+f(n)$
* $a \geqslant 1, b > 1$
* $f(n)$渐近趋正（存在$n_0$，当$n \geqslant n_0$时，$f(n)>0$）

步骤  

1. 比较$f(n)$和$\log_ba$（只考虑渐进的情况，$\log_ba$是叶子结点数量）
2. 判定关系$<, =, >$
	* 情况1：对于某个$\epsilon>0$，$f(n)=O(n^{(\log_ba) - \epsilon})$，即$f(n)$多项式地小于$n^{\log_ba}$，比$o$符号还要小。  
	$\Rightarrow T(n)=\Theta(n^{\log_ba})$  
	* 情况2：对于某个$k \geqslant 0$，$f(n)=\Theta(n^{\log_ba}{\lg}^kn)$【${\lg}^kn$表示$(\log_2n)^k$】，
	即$f(n)$基本等于$n^{\log_ba}$  
	$\Rightarrow T(n)=\Theta(n^{\log_ba}{\lg}^{k+1}n)$
	* 情况3：对于某个$\epsilon>0$，$f(n)=\Omega(n^{(\log_ba)+\epsilon})$，且对于某个$\epsilon'$，
	$af(\frac{n}{b}) \leqslant (1-\epsilon')f(n)$，即$f(n)$多项式地大于$n^{\log_ba}$，且随着递归树层次的增加，
	上层的$f(n)$大于下层所有$f(\frac{n}{b})$的和【否则将所有$f(n)$累加会得到无穷大】  
	$\Rightarrow T(n)=\Theta(f(n))$  
	以上3条即为主定理。

## 练习  

1. $T(n)=4T(\frac{n}{2})+n$   
	+ $a=4, b=2, f(n)=n$  
	+ $n^{\log_ba}=n^2，f(n)多项式地小于n^{\log_ba}$，属于情况1  
	+ $\Rightarrow T(n)=\Theta(n^2)$

2. $T(n)=4T(\frac{n}{2})+n^2$
	+ 属于情况2，其中k=0
	+ $\Rightarrow T(n)=\Theta(n^2 \lg n)$

3. $T(n)=4T(\frac{n}{2})+n^3$
	+ 属于情况3
	+ $\Rightarrow T(n)=\Theta(n^3)$

4. $T(n)=4(\frac{n}{2})+\frac{n^2}{\lg n}$
	+ 无法通过主方法求解，或许可以通过递归树求解

# 主定理的证明思路
将$T(n)=aT(\frac{n}{b})+f(n)$以递归树形式展开
![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_3_master_principle_provement.png)  
树高h：$\log_bn$  
叶子节点个数：$a^h=a^{\log_bn} = n^{\log_ba}$
计算成本

| 层数 | 计算成本 |
| :------ |:------|
| 1 | $f(n)$ |
| 2 | $af(\frac{n}{b})$ |
| 3 | $a^2f(\frac{n}{b^2})$ |
| $\dots$ | $\dots$ |
| k | $a^kf(\frac{n}{b^k})$ |
| $\dots$ | $\dots$ |
| $\log_bn$ | $$\Theta(n^{\log_ba})$$ |

分情况讨论

* 情况3：由于$f(n)$多项式地大于$\Theta(n^{\log_ba})$，且$af(\frac{n}{b}) \leqslant (1-\epsilon')f(n)$，计算成本随层数增加呈几何级数降低，所以如果将所有层的计算成本相加，式子中的最高次项应为$f(n)$  
$$\Rightarrow T(n)=\Theta(f(n))$$
* 情况1：由于$f(n)$多项式地小于$\Theta(n^{\log_ba})$，所以随着层数的增加，计算成本呈几何级数增长（有可能会增长得更快），几何级数中的最高次项为$\Theta(n^{\log_ba})$。  
$$\Rightarrow T(n)=\Theta(n^{\log_ba})$$
* 情况2：由于$f(n)=\Theta(n^{\log_ba}{\lg}^kn)$，顶层$f(n)$与底层$\Theta(n^{\log_ba})$基本相等，观察到从顶层到底层计算成本的变化是很有规律的，所以每一层的计算成本都基本一致，只是随着层数的增加，${\lg}^kn$逐渐消失了。  
$$\begin{aligned}
\Rightarrow T(n) & = 其中一层的计算成本 * 树高 \\
& = f(n) \log_bn \\ 
& = f(n) \Theta(\lg n) \\
& = n^{\log_ba}{\lg}^kn\Theta(\lg n) \\
& = \Theta(n^{\log_ba}{\lg}^kn\lg n) \\
& = \Theta(n^{\log_ba}{\lg}^{k+1}n)
\end{aligned}$$
