---
title: 关于hall定理
cwd: /home/amano-sei/hexo/myblog
date: 2020-11-29 15:09:19
mathjax: true
tags:
- 二分图匹配
---

> 设$G=\left\(V,E\right\)$是结点划分为$V=L\cup R$的无向二分图，其中$\left|L\right|=\left|R\right|$。对于任意$X\subseteq V$，定义$X$的邻居为：$N\left\(X\right\)=\left\\\{y\in V:对某个x\in X，\left\(x,y\right\)\in E\right\\\}$，
> Hall定理: 图$G$中存在一个完全匹配当且仅当对于每个子集$A\subseteq L$，有$\left|A\right|\leq\left|N\left\(A\right\)\right|$

首先hall定理的必要性显然（反证，鸽巢定理），下面列出几种充分性的证明。

一、通过证明最小割的容量为$\left|L\right|$。

　设任意切割$\left\(S,T\right\)$，其中$S$一定可以表示成$\left\\\{s\right\\\}\cup L'\cup R'$，$L'\subseteq L$，$R'\subseteq R$，则$c\left\(S,T\right\)$的边由$s$到$L\-L'$，$L'$到$R\-R'$，$R'$到$t$组成。其中$s$到$L\-L'$部分的容量等于$\left|L\right|\-\left|L'\right|$，$R'$到$t$部分的容量等于$\left|R'\right|$。由于$\left|N\left\(L'\right\)\right|\geq\left|L'\right|$，且其中最多有$|R'|$个点是$R'$中的，那么就有最后一部分的容量最少为$\left|N\left\(L'\right\)\right|\-\left|R'\right|\geq\left|L'\right|\-\left|R'\right|$，我们把这三部分加起来就有$c\(S,T\)\geq\left|L\right|$，然后又由于存在切割$\(\left\\\{s\right\\\},V\-\left\\\{s\right\\\}\)$其容量为$\left|L\right\|$，所以最小割的容量为$\left|L\right|$。由CLRS中的推论26\.11，得知存在一个基数为$\left|L\right|$的匹配，即一个完全匹配。

二、反证法

hall定理的反证有多种表现形式，例如某课本上说的找出"所有"交错路径，但是他们的本质都非常接近。下面描述一种比较直观的反证法。

　假设$G$的最大匹配$M$不是完全匹配，即$\left|M\right|\<\left|L\right|$，则至少存在一个点$l\_1\in L$没有被匹配，由题设，存在至少一个点$r\_1\in R$与之相邻，我们令$L\_1=\left\\\{l\_1\right\\\}$，$R\_1=\left\\\{r\_1\right\\\}$。由推论26\.11和最大流最小割定理知$r\_1$一定被匹配了，不然我们就找到了一条增广路径$\left\(s\rightarrow l\_1\rightarrow r\_1\rightarrow t\right\)$。则存在一个$l\_2\in L且l\_2\notin L\_1$，有$\left\(l\_2,r\_1\right\)\in M$，则在残存网络中$l\_1$可达$l\_2$。
　我们令$L\_2=L\_1\cup\left\\\{l\_2\right\\\}$，则有$L\_2$中所有的点$l\_1$都可达，又由于$\left|N\left\(L\_2\right\)\right|\geq\left|L\_2\right|\>\left|R\_1\right|$，所以存在至少一个点$r\_2\in R且r\_2\notin R\_1$，由$L\_2$中的点可达，且相连的边不是匹配边（因为在$R$中与$L\_2$中的点匹配的点都在$R\_1$中），令$R\_2=R\_1\cup\left\\\{r\_1\right\\\}$。由推论26\.11和最大流最小割定理$r\_2$也一定被匹配了，存在$l\_3\in L且l\_3\notin L\_2$，有$\left\(l\_3,r\_2\right\)\in M$，则在残存网络中$l\_1$可达$l\_3$。
　然后我们重复前述，直到$L\_\{\left|L\right|\}$，我们找到$r\_\{\left|L\right|\}$，它也一定被匹配，这就使得出现了$\left|L\right|$个点被匹配，这与假设矛盾，综上证毕。

三、归纳法

这个证明方向我虽然开始有考虑过，但是没有想到实际的方法。下面的内容是从[这里](https://blog.csdn.net/ChrysanthemumZhao/article/details/80723793)看到后修改得来的。

　当$\left|L\right|=1$时，显然。

　假设对于$\left|L\right|\<n$时，hall定理正确，我们分两种情况来看。
　　对于任意的非空真子集$A\subset L$，总有$\left|A\right|\<\left|N\left\(A\right\)\right|$。任取一个点$l\in L$，则一定存在$r\in R$使得$\left\(l,r\right\)\in E$，我们删去$l和r$以及所有与之相连的边，对于新图$G'=\left\(V',E'\right\)$，$V'=L'\cup R'$，有任意的$A\subseteq L'$，$\left|N\left\(A\right\)\right|$较之前最多减少$1$,则有$\left|A\right|\leq\left|N\left\(A\right\)\right|$，由假设$G'$存在一个完全匹配，再把$\(l,r\)$加入$G'$的完全匹配得到$G$的一个完全匹配。
　　存在至少一个非空真子集$A\subset L$，使得$\left|A\right|=\left|N\left\(A\right\)\right|$。由定义知$N\left\(X\cup Y\right\)=N\left\(X\right\)\cup N\left\(Y\right\)$，则有$\left|N\left\(X\cup Y\right\)\right|=\left|N\left\(X\right\)\right|\+\left|N\left\(Y\right\)\right|\-\left|\left\(N\left\(X\right\)\cap N\left\(Y\right\)\right\)\right|$。所以对于任意的$B\subseteq \left\(L\-A\right\)$，$\left|A\right|\+\left|B\right|=\left|A\cup B\right|\leq\left|N\left\(A\cup B\right\)\right|=\left|N\left\(A\right\)\right|\+\left|N\left\(B\right\)\right|\-\left|\left\(N\left\(A\right\)\cap N\left\(B\right\)\right\)\right|$，则有$\left|B\right|\leq\left|N\left\(B\right\)\right|\-\left|\left\(N\left\(A\right\)\cap N\left\(B\right\)\right\)\right|\leq\left|N\left\(B\right\)\right|$，我们做两个新图$G\_1=\left\(V\-A\-N\left\(A\right\), E\_1\right\)$和$G\_2=\left\(A\cup N\left\(A\right\), E\_2\right\)$由于两个新图都满足假设，所以两个新图都存在完全匹配，我们再把两个新图的完全匹配合并，得到原图的完全匹配。

　综上，得证。

