---
title: MIT算法导论笔记(3):求解递归式
tags:
  - 算法
categories: 技术
date: 2017-09-24 18:56:33
mathjax: true 
---
至今为止，求解递归式还没有一种通用的方法。  
本文介绍求解递归式的三个主要方法：代换法，递归树法，主定理法。
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
