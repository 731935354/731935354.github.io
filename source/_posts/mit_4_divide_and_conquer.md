---
title: MIT算法导论笔记(4):分治法
tags:
  - 算法
categories: 技术
date: 2017-10-02 15:00:00
mathjax: true
---

分治法是计算机科学中很重要的算法，其核心思想是将一个复杂的问题分解成若干个子问题，对子问题逐个击破，最后将结果合并。
主要步骤: 

1. **分**：把一个问题的特殊实例划分成若干子问题（子问题规模应比原问题更小）。
2. **治**：递归地解决每一个子问题。
3. **合**：将子问题的解合并。

<!--more-->

以下为应用分治法的几个经典案例
#归并排序
##步骤
1. 将待排序数组一分为二。
2. 递归地对每一个子数组进行排序。
3. 合并两个有序数组。

##运行时间分析
$T(n)=2T(\frac{n}{2})+\Theta(n)$

* $2T(\frac{n}{2})$实际上应该是$T(\frac{n}{2})$的**上界**与**下界**相加，但此处可以将上界与下界省掉（具体将在下节介绍）
* 2为子问题的数量
* $\Theta(n)$为附加的计算量，即不包含递归的部分，此例为合并数组的计算量。

根据主定理，属于情况2，$T(n)=\Theta(n^{\log_ba}{\lg}^{k+1}n)$，其中a=2，b=2，k=0。    
$\Rightarrow T(n)=\Theta(n\lg n)$

#二分查找
##问题描述
在一个有序数组中寻找元素x。
##步骤
* 分：将x与数组中央元素比较
* 治：在其中一个子数组中递归查找
* 合：无

##运行时间分析
$T(n)=T(\frac{n}{2})+\Theta(1)=\Theta(\lg{n})$

#乘方问题
##问题描述
给定一个实数x和一个正整数n，计算$x^n$。
##朴素算法
将x连乘n次，时间复杂度$\Theta(n)$。
##分治法
$$x^n=
\begin{cases}
x^{\frac{n}{2}} \cdot x^{\frac{n}{2}} \quad \text{(n为偶数)} \\
x^{\frac{n-1}{2}} \cdot x^{\frac{n-1}{2}} \cdot x \quad \text{(n为奇数)}
\end{cases}
$$

###运行时间分析
$T(n)=T(\frac{n}{2})+\Theta(1)=\Theta(\lg{n})$

#裴波那契数
##定义
$$F(n)=
\begin{cases}
0 & n=0 \\
1 & n=1 \\
F(n-1)+F(n-2) & n \geqslant 2 \\
\end{cases}
$$
##递归算法
$T(n)=\Omega({\phi}^n)$，$\phi=\frac{1+\sqrt{5}}{2}$（黄金比例数），即**指数级**运行时间。
##自下而上算法
如果把递归算法按递归树的形式展开，会发现其中有很多重复的子树，这是递归算法运行时间长的主要原因。因此，如果依次计算$F(0),F(1),F(2) \dots$，就可以把运行时间降低为线性复杂度。  
$T(n)=\Theta(n)$
##朴素平方递归
$F(n)=round({\phi}^n/\sqrt{5})$，round表示取最接近的整数
###理论复杂度

* 求${\phi}^n$：$\Theta(\lg{n})$
* 除以$\sqrt{5}$：$\Theta(1)$
* 取最接近的整数：$\Theta(1)$

###理论复杂度实际不可行的原因
* $\phi$和$\sqrt{5}$需要用浮点数来表示，浮点数的精确位数是有限的，因此可能会得到错误的答案。
* $\phi \cdot \phi$运算无法在常数时间内完成。

##平方递归
###定理
$$
 {\begin{matrix}
\begin{pmatrix} F(n+1) & F(n) \\ F(n) & F(n-1) \end{pmatrix} 
= 
{\begin{pmatrix} 1 & 1 \\  1 & 0 \end{pmatrix}}^n
\end{matrix}}
$$
###运行时间
$T(n)=\Theta(\lg{n})$
###利用数学归纳法证明定理

* 基本情况 
$$
 {\begin{matrix}
{\begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}}^1 
= 
\begin{pmatrix} F(2) & F(1) \\  F(1) & F(0) \end{pmatrix}
\end{matrix}}
$$
* 假设
$$
 {\begin{matrix}
\begin{pmatrix} F(k+1) & F(k) \\ F(k) & F(k-1) \end{pmatrix} 
= 
{\begin{pmatrix} 1 & 1 \\  1 & 0 \end{pmatrix}}^k
\end{matrix}}
$$
* 推导
$$
{\begin{matrix}
\begin{pmatrix} F(n+1) & F(n) \\ F(n) & F(n-1) \end{pmatrix} 
= 
\begin{pmatrix} F(n) & F(n-1) \\  F(n-1) & F(n-2) \end{pmatrix}
\end{matrix}} \cdot X
$$
根据假设和裴波那契数的定义，可以求出
$$
X=\begin{pmatrix} 1 & 1 \\  1 & 0 \end{pmatrix}
$$
即
$$
\begin{matrix}
\begin{pmatrix} F(n+1) & F(n) \\ F(n) & F(n-1) \end{pmatrix} 
= 
{\begin{pmatrix} 1 & 1 \\  1 & 0 \end{pmatrix}}^{n-1} \cdot \begin{pmatrix} 1 & 1 \\  1 & 0 \end{pmatrix}
=
{\begin{pmatrix} 1 & 1 \\  1 & 0 \end{pmatrix}}^n
\end{matrix}
$$
得证。

#矩阵乘法
##问题描述
给定方阵$A=[a_{ij}]$和$B=[b_{ij}]$，$(i,j = 1,2,\dots, n)$，求矩阵$C$，$C=A \cdot B$  
$c_{ij}=\sum_{k=1}^{n} a_{ik} \cdot b_{kj}$
##标准解法$\Theta(n^3)$
```
for i ← 1 to n
	for j ← 1 to n
		c[i][j] ← 0
		for k ← 1 to n
			c[i][j] ← c[i][j] + a[i][k] + b[k][j]
```
##分治法
###思路
将原矩阵视为$2 \times 2$的分块矩阵，其中每一块都是$\frac{n}{2} \times \frac{n}{2}$子矩阵。  
$C=A \times B$改写成
$$
\begin{matrix}
\begin{bmatrix} r & s \\  t & u \end{bmatrix}
=
\begin{bmatrix} a & b \\  c & d \end{bmatrix}
\times
\begin{bmatrix} e & f \\  g & h \end{bmatrix}
\end{matrix}
$$
其中  
$r=ae+bg$  
$s=af+bh$  
$t=ce+dg$  
$u=cf+dh$  
###运行时间分析
8次规模为$\frac{n}{2} \times \frac{n}{2}$的子矩阵的递归乘法：递归  
4次矩阵加法：$\Theta(n^2)$  
$T(n)=8T(n/2)+\Theta(n^2)$  
由主定理  
$n^{\log_ba} = n^{\log_28} = n^3$  
$n^2$多项式的小于$n^3$，属于情况1  
$T(n)=\Theta(n^{\log_ba})=\Theta(n^3)$
运行时间相比标准解法并没有提高
##斯特拉森算法
###改进思路
由上面的运行时间分析可知，矩阵乘法的代价明显高于矩阵加法的代价，因此要想办法减少矩阵乘法的次数，即使矩阵加法次数变多也无所谓。  
尝试将上面的8次矩阵乘法降低到7次。  
###算法描述
$P_1=a \cdot (f-h)$  
$P_2=(a+b) \cdot h$  
$P_3=(c+d) \cdot e$  
$P_4=d \cdot (g-e)$  
$P_5=(a+d) \cdot (e+h)$  
$P_6=(b-d) \cdot (g+h)$  
$P_7=(a-c) \cdot (e+f)$  
$r=P_5+P_4-P2+P6$  
$s=P_1+P_2$  
$t=P_3+P_4$  
$u=P_5+P_1-P_3-P_7$

1. 分解$A$和$B$，计算所有的项【$(a+b)$,$(c+d)$等】——$\Theta(n^2)$
2. 递归计算所有的$P_i$，即$P_1，P_2，\dots P_7$
3. 计算r，s，t，u——$\Theta(n^2)$

###运行时间分析
$T(n)=7T(\frac{n}{2})+\Theta(n^2)$  
根据主定理，$n^{\log_ba}=n^{\log_27}$多项式的大于$n^2$，属于情况1  
$\Rightarrow T(n)=\Theta(n^{\lg{7}})=O(n^{2.81})$   
目前最好的算法运行时间为$O(n^{2.376})$ 

#超大规模集成电路布局
##问题描述
将一个有n个叶子的完全二叉树嵌入到网格中，使得占用的面积最小。  
**完全二叉树**：每一层节点数都为2的幂。  
**叶子**：树的最底层的节点。  
**嵌入网格**：二叉树的节点要放在网格的顶点上，连接两点的边不可交叉。
##朴素算法
![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_4_embed_complete_binary_tree.png)
$H(n)=H(\frac{n}{2})+\Theta(1)=\Theta(\lg{n})$  
$W(n)=2W(\frac{n}{2})+O(1)=\Theta(n)$  
面积=$\Theta(n\lg{n})$
##将$\Theta(n\lg{n})$改进为$\Theta(n)$
目标

* $H(n)=\Theta(\sqrt{n})$
* $W(n)=\Theta(\sqrt{n})$

$\Rightarrow 面积=\Theta(n)$

思路：根据主定理逆推，$T(n)=aT(\frac{n}{b})+f(n)$，当$n^{\log_ba}=n^{\frac{1}{2}}$且$f(n)$多项式地小于$\sqrt{n}$时，$T(n)=\Theta(\sqrt{n})$。  
尝试构建递归式：$T(n)=2T(\frac{n}{4})+\Theta(n^{\frac{1}{2}-\epsilon})$

![这里写图片描述](http://owruh8822.bkt.clouddn.com/mit_4_H_layout.png)

$L(n)=2L(\frac{n}{4})+\Theta(1)=\Theta(\sqrt{n})$
