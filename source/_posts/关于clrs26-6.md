---
title: 关于clrs26-6
cwd: /home/amano-sei/hexo/myblog
date: 2021-01-13 01:45:23
mathjax: true
tags:
- 算法导论
- 二分图匹配
- 论文翻译
---

（因为睡不着，所以爬起来写题解）

首先是[参考论文](/pdf/Hopcroft-Karp.pdf)[<sup>1</sup>](#refer)，其实论文关于算法本身介绍的非常详细。

**a.**也是论文里的引理1，证明的话我们可以从匹配的定义出发。令$\left|P\right|=2k\+1$，显然$P$中有$k\+1$条边属于$E\-M$，其余$k$条边属于$M$，则$\left|M\oplus P\right|=\left|M\-P\right|\+\left|P\-M\right|=\left|M\right|\-k\+k\+1=\left|M\right|\+1$。对于不在$P$中出现过的点我们不需要关心，因为它不会改变没有出现过的点的度数。对于$P$两端的未匹配点，显然在$P$中分别有且只有1条（可能是同一条）没有在$M$中出现的边与之对应，显然这两点在$\left\(M\-P\right\)\cup\left\(P\-M\right\)$只分别有且只有一条边与之匹配。对于$P$内部的每个匹配点，显然在$P$有一条属于$M$的匹配边和一条不属于$M$的非匹配边，那么最终在$\left\(M\-P\right\)\cup\left\(P\-M\right\)$中有且只有一条不属于$M$的边与该点连接。类似的对于多个点不相交的$P\_i$，因为互相点不相交，所以互相不会影响，我们只需要分别考虑每个$P\_i$即可，类似前面的论述，结论得证。

**b.**也是论文中的定理1，论文中有一个很优雅且巧妙的证明。我们设$\overline\{G\}=\left\(V,M\oplus M^\*\right\)$因为$M$和$M^\*$都是匹配，所以对于每个点在$M\-M^\*$中至多有$1$条边与之相连，在$M^\* \-M$中至多有$1$条边与之相连，即$\overline\{G\}$中每个点的度数要么是0，要么是1，要么是2。对于这样的图每个连通块有3种可能，要么是个孤立点（度数为$0$），要么是一个环（每个点度数都是$2$），要么是一条路径（两端度数为$1$，内部度数为$2$）。我们设$\overline\{G\}$中的连通分量分别为$C\_1, C\_2, ..., C\_g$，$C\_i=\left\(V\_i, E\_i\right\)$。我们定义$\delta\left\(C\_i\right\)=\left|E\_i\cap M^\*\right|\-\left|E\_i\cap M\right|$，显然$\delta\left\(C\_i\right\)$只可能取$\-1, 0, 1$这三个值（因为边只能交替属于$M$和$M^\*$），并且当且仅当$\delta\left\(C\_i\right\)=1$时，$C\_i$是一条增广路径。由于$\sum\limits\_\{i=1\}^g \delta\left\(C\_i\right\)=\left|M^\* \-M\right|\-\left|M\-M^\*\right|=\left|M^\*\right|\-\left|M\right|$，所以至少有$\left|M^\*\right|\-\left|M\right|$个$C\_i$有$\delta\left\(C\_i\right\)=1$，即至少有这么多增广路径，又因为连通分量点互不相交，所以有这些增广路径结点不相交。

**c.**我们用反证法，由于$P$与$P\_i$之间没有公共点，所以$P$也是$M$的增广路径，显然$\left|P\right|\geq l$，那么如果$\left|P\right|\le l$即$\left|P\right|=l$，那么$\left\\\{P\_1, P\_2, ..., P\_k, P\right\\\}$就是一个更大的不相交最短增广路径集，这不符合$\left\\\{P\_1, P\_2, ..., P\_k\right\\\}$是极大集合的定义，矛盾，故得证。

**d.**c和d一起其实是论文里推论4的另一种表现形式，这么做回避了比较抽象的推论4的证明，不过其实也不差啥。（对称差的证明省略了，我自己是证明了一遍结合律交换律以及自己和自己做对称差是空集）由于a的结论，我们知道$\left|M'\right|=\left|M\right|\+k$，$\left|M'\oplus P\right|=\left|M'\right|\+1=\left|M\right|\+k\+1$，由b我们知道$A=M'\oplus P\oplus M=\left\(P\_1 \cup P\_2 \cup ...\cup P\_k\right\)\oplus P$至少包含$\left\(k\+1\right\)$条点不相交的关于$M$的增广路径，由最短增广路径的长度为$l$，所以显然$\left|A\right|\geq\left\(k\+1\right\)l$。
接下来我们来证明后半，由于$P\_1, P\_2, ..., P\_k$之间没有公共点，所以若$i\neq j$则$P\_i \cap P\_j =\emptyset$，则$\left|A\right|=\left|P\_1 \cup P\_2 \cup ... \cup P\_k\right|\+\left|P\right|\-\left|\left\(P\_1 \cup P\_2 \cup ... \cup P\_k\right\)\cap P\right|=kl\+\left|P\right|\-\left|\left\(P\_1 \cup P\_2 \cup ... \cup P\_k\right\)\cap P\right|\geq \left\(k\+1\right\)l$，即$\left|P\right|\geq l\+\left|\left\(P\_1 \cup P\_2 \cup ... \cup P\_k\right\)\cap P\right|$由于$P$与$P\_1, P\_2, ..., P\_k$有公共点，所以$\left\(P\_1 \cup P\_2 \cup ... \cup P\_k\right\)\cap P \neq\emptyset$，则$\left|P\right|\>l$，得证。

**e.**我们设最大匹配为$M'$，则$M'\oplus M$中至少包含$\left|M'\right|\-\left|M\right|$条点不相交的增广路径，显然他们的长度都大于等于$l$，即每条增广路径至少包含$l\+1$个点，由于点不相交所以最多有$\dfrac\{\left|V\right|\}\{l\+1\}$条增广路径，则有$\left|M'\right|\-\left|M\right|\leq \dfrac\{\left|V\right|\}\{l\+1\}$，即$\left|M'\right|\leq\left|M\right|\+\dfrac\{\left|V\right|\}\{l\+1\}$，即得证。

**f.**在第$\sqrt\{V\}$次迭代后增广路径的长度至少为$\sqrt\{V\}$，即最大匹配的规模不会超过为$\left|M\right|\+\dfrac\{\left|V\right|\}\{\sqrt\{V\}\}=\left|M\right|\+\sqrt\{V\}$，由于每次迭代至少找到一条增广路径，所以至多还能再迭代$\sqrt\{V\}$次，即总共至多$2\sqrt\{V\}$次。
e和f一起完成了原论文中定理3的作用，虽然其实不影响，但是我感觉规范来说还是应该用取整得到的整数，原论文中的不等式居然卡了我，为什么我这么蠢呢...

**g.**首先bfs出来所有要用的边和点，然后用一个不会取消book，并且找到一条路径即刻返回的dfs即可。其实也是我见到这道题的最初也是最大的疑惑点...怎么可能有轻易找到最大不相交集的办法呢，但是这里是极大，再次点名批评翻译（

<div id="refer"></div>
- [1] Hopcroft, John E., and Richard M. Karp. "An n^5/2 algorithm for maximum matchings in bipartite graphs." SIAM Journal on computing 2.4 (1973): 225-231.

