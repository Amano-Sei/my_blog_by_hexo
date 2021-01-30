---
title: 关于tarjan算法
cwd: hexo/myblog
date: 2020-10-09 10:51:36
mathjax: true
tags:
- tarjan算法
- 论文翻译
- 深度优先搜索
- 强连通分量
- 点双连通分量
---

tarjan算法其实是在dfs基础上维护一个标志值来判断当前点是否是某种特殊点，而学习tarjan算法，通过tarjan的[DEPTH-FIRST SEARCH AND LINEAR GRAPH ALGORITHMS](/pdf/tarjan.pdf)学习好像并不是什么好的选择...但是读一读dalao的论文还是挺有趣的，本文对tarjan的论文进行简单翻译和补充我自己的理解。

**一些符号和基础定义**: 
对于图$G=\(V,E\)$来说。
路径$p:v\stackrel\{\*\}\{\Rightarrow\}w$表示存在一个从$v$到$w$的点和边的序列。
如果一条路径上所有的点都互不相同，则我们称其为简单路径\(simple path\)。
路径$p:v\stackrel\{\*\}\{\Rightarrow\}v$是一个闭合回路\(closed path\)。
一个闭合回路$p:v\stackrel\{\*\}\{\Rightarrow\}v$如果它的所有边各不相同，并且在$p$中只有$v$出现了两次，那么我们称p为一个环\(cycle\)。
如果两个环它们的循环排列互相相同，那么我们把两个环视为同一个环。
如果无向图中所有的点对之间都有一条路径，那么它是连通的\(connected\)。

对于一棵\(有向根\)树$T$来说。
无向版本的$T$是连通的，并且有一个根节点它不是任何边的终点\(head\)，而除了根节点外的所有点都是且只是某一条边的终点。
$v\rightarrow w$表示"$\(v,w\)$是$T$中的一条边"。
$v\stackrel\{\*\}\{\rightarrow\}w$表示"在$T$中存在一条从$v$到$w$的路径"。
如果$v\rightarrow w$，那么$v$是$w$的父节点\(father\)，$w$是$v$的子节点\(son\)。
如果$v\stackrel\{\*\}\{\rightarrow\}w$，那么$v$是$w$的祖先节点\(ancestor\)，$w$是$v$的后代节点\(descendant\)。
每个点都是自己的祖先节点和后代节点。
如果$v$是$T$中的一个点，那么$T\_v$是由$v$的所有后代节点组成的"$T$的子树"。
如果$G$是一个有向图，如果树$T$包含$G$中所有的点并且$T$是$G$的子图，那么$T$是$G$的一个生成树。

**定义1**: $G=\(V,E\)$是一个图，对于每个点$v\in V$我们可以构造一个包含所有的有$\(v,w\)\in E$的点$w$组成的列表，我们称这样的列表为$v$的邻接表; 对于图$G$的所有点的邻接表构成的集合，我们称之为图$G$的邻接结构。

这就是我们熟悉的邻接表，没什么好说的。

**定义2**: 设$P$是一个有向图，它的边集由两个不相交的集合组成，分别用$v\rightarrow w$和$v\dashrightarrow w$表示其中的边，假定P满足以下性质:
　　\(i\) 包含$v\rightarrow w$的边构成的子图$T$是$P$的一棵生成树。
　　\(ii\) $\dashrightarrow \subseteq \left\(\stackrel\{\*\}\{\rightarrow\}\right\) ^\{\-1\}$，简单来说如果有$u\dashrightarrow v$，那么一定有$v \stackrel\{\*\}\{\rightarrow\} u$，对于所有P中没有在T中的边，都把某个点连接到了它在T中某个祖先节点上。
那么$P$就是一个palm树，我们把所有$v\dashrightarrow w$这样的边称为$P$的$frond$。

我们可以看到palm树只有祖先和后代之间可能有边，借助这一性质可以帮助我们完成很多事情。

**定理1**: $P$是无向连通图$G$通过dfs生成的有向图，那么$P$是一棵palm树; 相反地，任何palm树$P$都可以由无向版本的$P$通过dfs得来。

我们定义palm树这样的结构就是为了和dfs建立联系，下面我们给出一种构造的算法。

**算法1**:
```
BEGIN
    INTEGER i;
    PROCEDURE DFS(v, u); COMMENT vertex u is the father of
                         vertex v in the spanning tree being constructed;
        BEGIN
            NUMBER(v) := i := i+1;
            FOR w in the adjacency list of v DO
                BEGIN
                IF w is not yet numbered THEN
                    BEGIN
                        construct arc $v->w$ in P;
                        DFS(w, v);
                    END
                ELSE IF NUMBER(w) < NUMBER(v) and w != u
                    THEN construct arc $v-->w$ in P;
            END;
        END;
    i := 0;
    DFS(s, 0);
END;
```

需要注意$NUMBER\(w\)\<NUMBER\(v\)$是必要的条件，避免祖先再沿着$frond$的反向指向后代。关于这个构造的正确性，因为它本身是不反直觉的，所以我只说下为什么这样构造出来的$frond$都是指向祖先的。如果$\(v,w\)$中$w$已经被number了，那么它有两种情况，一种是$NUMBER\(v\)\<NUMBER\(w\)$，一种是$NUMBER\(v\)\>NUMBER\(w\)$。
　　我们先看$NUMBER\(v\)\>NUMBER\(w\)$，如果$w$不是$v$的父亲的话，我们有$\(v,w\)$一定早于$\(w,v\)$被检查（不然$w$就是$v$的父亲），但是$w$比$v$先被number，所以唯一的可能是$w$还没有从$DFS\(w,\-\)$中返回，那么显然$v$是$w$的后代，这就是我们添加进的$frond$。
　　我们再看另一种情况，这里的$w$比$v$晚number，但是我们还在$DFS\(v,\-\)$中，所以显然$w$是$v$已经返回的后代，这种情况下我们在$DFS\(w,\-\)$中已经有了$frond$ $\(w,v\)$，所以这种情况我们会进行丢弃。

**推论2**: 任何无向图$G$都可以通过把边以适当的方式有向化转换为一个palm树。

其实就是定理1的另一种说法。

**定义3**: $G=\(V,E\)$是一个无向图。对于$V$中任意互不相同三点$v$,$w$,$a$，存在一条路径$p:v\stackrel\{\*\}\{\Rightarrow\}w$不经过$a$，那么$G$是点双连通的。另一方面如果$V$中存在互不相同的三点$v$,$w$,$a$，有$a$总是在任何$p:v\stackrel\{\*\}\{\Rightarrow\}w$的路径上，且最少存在一条路径，那么$a$就是图$G$的割点。

**引理3**: 无向图$G=\(V,E\)$,我们定义一种在边集中的等价关系: 当且仅当两边属于同一个环时，两边是等价的。我们让不同的等价类分别为$E\_i$，$1\leqq i\leqq n$，并且$G\_i=\(V\_i, E\_i\)$，$V\_i$是$E\_i$中的点的集合; $V\_i=\left\\\{ v| \exists w\(\(v, w\)\in E\_i\)\right\\}$，那么:
　　\(i\) 对于每个$1\leqq i\leqq n$，$G\_i$是点双连通的。
　　\(ii\) 没有$G\_i$是某个点双连通图$G$的真子图。
　　\(iii\) 图$G$的每个割点在所有$V\_i \(1\leqq i\leqq n\)$中一共至少出现两次。图$G$的每个非割点在所有$V\_i \(1\leqq i\leqq n\)$中一共出现且只出现一次。
　　\(iv\) 对于任意的$1\leqq i,j\leqq n$，$V\_i\cap V\_j$至多只包含一个点，且这个点是图的割点。
那么图$G$的子图$G\_i$被叫做$G$的点双连通分量。

我们给出了割点的定义和点双连通分量的定义，为了便于寻找点双连通分量，我们再给出$LOWPT$的定义。

$LOWPT\(v\)$是集合$\left\\\{v\right\\\}\cup\left\\\{w|v\stackrel\{\*\}\{\rightarrow\}\dashrightarrow w\right\\\}$中最小的点。即$LOWPT\(v\)$是最小的从v经过0或更多条树边再加1条$frond$可达的最小的点。

**引理4**: 对于无向图$G$，$P$是有向化$G$中的边形成的palm树，$T$是$P$的生成树，假定$p:v\stackrel\{\*\}\{\Rightarrow\}w$是在$G$中任意一条这样的路径，那么$p$中包含一个点，且这个点是$v$和$w$在$T$中的祖先节点。

设以$u$为根的$T\_u$是$T$中包含$p:v\stackrel\{\*\}\{\Rightarrow\}w$中所有节点最小的子树，如果$u=v$或是$u=w$，那么显然。否则我们假设$T\_\{u\_\{1\}\}$和$T\_\{u\_\{2\}\}$是两棵包含了$p$上点的子树，并且$u\rightarrow u\_1$, $u\rightarrow u\_2$（即$u\_1$和$u\_2$是$u$的儿子）。如果只有一棵这样的子树，那么由于$T\_u$是最小的会使得$u$是路径上的点（反证）；如果两棵这样的子树存在，那么路径$p$如果想要从$T\_\{u\_\{1\}\}$到$T\_\{u\_\{2\}\}$就必须要经过u，因为无论是$\rightarrow$还是$\dashrightarrow$都与祖先节点相连，而两棵子树中任何点都不可能是另一棵子树中的点的祖先。而$u$是两者所有节点的祖先节点，并且$u$是$T\_u$中唯一满足这个条件的点，所以$u$一定会在$p$上。

**引理5**: 让$G$是一个无向连通图，$P$是有向化$G$中的边后形成的palm树，$T$是$P$的生成树。假定$a$,$v$,$w$是图$G$中三个不同的点，并且有$\(a,v\)\in T$，$w$不是$v$在$T$中的后代节点（即在$T$中不存在$\(v\stackrel\{\*\}\{\rightarrow\}w\)$）。如果$LOWPT\(v\)\geqq a$，那么$a$是$G$的一个割点，并且移除a将导致$v$和$w$断连。相反地，如果$a$是$G$的一个割点，那么存在点$v$和$w$满足上述的性质。

如果有$a\rightarrow v$，并且$LOWPT\(v\)\geqq a$，那么任何与$v$相关的路径（无论是$v$出发，还是以$v$为终点）如果不经过$a$的话，那么只能在子树$T\_v$中，但是这棵子树是没有点$w$的，第一部分得证。（其实这里一开始我产生了疑问，有向化的palm树没有路径并不能代表$G$没有路径，但是这里把这里的路径想成是无向版本palm树的路径即可）
为了证明反命题，我们设$a$是图$G$的一个割点。如果$a$是$P$的根，那么就最少有两条树边从$a$发出（不然显然去除$a$不会影响连通性，即与$a$是割点矛盾），我们设有两条边分别为$\(a,v\)$和$\(a,w\)$，那么显然$LOWPT\(v\)\geqq a$，并且$w$也不是$v$的后代；如果$a$不是$P$的根，那么考虑删除$a$后形成图$G$的连通分量，其中某一个连通分量一定只包含$a$的后代（如果没有这样的连通分量，那么由于除去$T\_a$即$a$的后代节点外的点的连通性都不受影响，图不会被分割成至少两个连通分量，这与$a$是割点矛盾）（并且这个连通分量只会包含一个$a$的子节点，由引理4，准确来说是类似引理4的证明），我们设$v$是这个连通分量中$a$的子节点，$w$是$v$的某个真祖先节点，那么我们有$a\rightarrow v$，$LOWPT\(v\)\geqq a$（否则$v$就会与$a$的真祖先节点之间可达），$w$不是$v$的后代节点。因此反命题得证。
这里的证明很重要，它使得"$a$有子节点$v\(LOWPT\(v\)\geqq a\)$且存在一个不是$v$的后代节点的点"成为了"$a$是割点"的充要条件，让我们很方便地判断一个点是否是割点。

**推论6**: $G$是连通无向图，$P$是有向化$G$的边后形成的palm树，$T$是$P$的生成树。如果$C$是$G$中一个点双连通分量，那么$C$中的点组成了$T$中的一棵子树，并且这棵子树的根要么是$G$的割点，要么是$T$的根。

这条推论把找点双连通分量的问题转化为了找割点的问题。这里我们给出一个更方便计算的$LOWPT$的式子，进而给出求点双连通分量的算法。
$$
LOWPT\(v\) = min\(\left\\\{NUMBER\(v\)\right\\\}\cup\left\\\{LOWPT\(w\)|v\rightarrow w\right\\\}\cup\left\\\{NUMBER\(w\)|v\dashrightarrow w\right\\\}\)
$$

**算法2**:
```
BEGIN
    INTEGER i;
    procedure BICONNECT(v, u);
        BEGIN
            NUMBER(v) := i := i+1;
            LOWPT(v) := NUMBER(v);
            FOR w in the adjacency list of v DO
                BEGIN
                    IF w is not yet numbered THEN
                        BEGIN
                            add (v, w) to stack of edges;
                            BICONNECT(w, v)
                            LOWPT(v) := min(LOWPT(v), LOWPT(w));
                            IF LOWPT(w) >= NUMBER(v) THEN
                                BEGIN
                                    start new biconnected component;
                                    WHILE top edge e = (u1, u2) on edge
                                          stack has NUMBER(u1)
                                          >= NUMBER(w) DO
                                        delete(u1, u2) from edge stack and
                                          add it to current component;
                                    delete (v, w) from edge stack and add
                                       it to current component;
                                END;
                        END
                    ELSE IF (NUMBER(w) < NUMBER(v)) and (w != u) THEN
                        BEGIN
                            add (v, w) to edge stack;
                            LOWPT(v) := min(LOWPT(v), NUMBER(w));
                        END;
                END;
        END;
    i:=0;
    empty the edge stack;
    FOR w a vertex DO IF w is not yet numbered THEN BICONNECT(w, 0);
END;
```

**定理7**: 该点双连通算法对于一个有$V$个点$E$条边的图需要$O\(V,E\)$的空间和时间。

这里的过程其实和前面的dfs别无二致，而这里的额外操作与$E$成比例，palm树上每条边都最多会入栈一次，所以显然。

**定理8**: 该点双连通分量算法对于任何无向图都可以正确给出所有的点双连通分量。

因为dfs的过程其实是对每个连通分量进行分析的，所以我们只需要证明连通图中的正确性即可。
我们用归纳证明。
当连通图$G$中没有边时，即$G$是空图或是只有1个点时。这个算法会正确地终止，并且列出没有点双连通分量，显然这种情况下它是正确的。
假定算法在连通图的边数小于等于$E\-1$时会得到正确结果，我们把算法应用到一个有$E$条边的连通图$G$上。
　　我们假设我们应用算法时会跑出来至少两个点双连通分量，我们设第一个点双连通分量为$G'$，显然当$G'$的边数小于$E$，那么我们可以先只用$G'$中的点和边，从其中$NUMBER$最小的点即$G'$的根节点出发，然后再把$G$中所有$G'$中除了$G'$的根节点以外的点及对应的边都删去，再从原本的根节点出发，这样我们会有和直接从根节点出发完全相同的结果，并且由边数小于$E$情况下结果是正确的保证正确性。（其实这里有着问题，对两个子图正确，不代表对子图合起来的图正确，但是我们知道$G'$的根节点是割点，那么一定有一个只包含它后代节点的点双连通分量，而$G'$就是这样的一个分量，补上这样的描述应该就够了）
　　我们接下来证明只有一个连通分量的情况下它是正确的，首先除了根节点以外的节点都不是割点这点已经由引理5保证了。我们用反证法，如果根节点是割点，那么它一定至少有两个子节点（不然不能满足引理5），但是我们知道算法运行时在检查根节点和子节点相连的那条边时，一定会生成一个新的连通分量。这与我们只有一个连通分量矛盾，故根节点也不是割点，即$G$是一个点双连通图。
综上得证。
tarjan这里的证明说实话巧妙得有点让人有点难以接受，但是它确实应该是正确的。

**定义4**: 有向图$G$，如果对于$G$中任何点对$v,w$都存在路径$p\_1:v\stackrel\{\*\}\{\Rightarrow\}w$和$p\_2:w\stackrel\{\*\}\{\Rightarrow\}v$，那么我们称图$G$是强连通的。

就是最普通的强连通图\(strongly connected graph\)定义。

**引理9**: 有向图$G=\(V,E\)$，我们定义点集上的一种等价关系: 对于点$v$和点$w$，若存在一条包含有$w$的路径$p:v\stackrel\{\*\}\{\Rightarrow\}v$，那么我们称$v$和$w$是等价的。我们可以$V$中的不同的等价类为$V\_i\(1\leqq i\leqq n\)\$。$G\_i = \(V\_i,E\_i\)$，$E\_i =\left\\\{\(v,w\)\in E|v,w\in V\_i\right\\\}$。那么:
　　\(i\) 每个$G\_i$都是强连通的。
　　\(ii\) 没有$G\_i$是某个强连通图的真子图。
那么我们称这些$G\_i$为$G$的强连通分量。

也是普通的强连通分量\(strongly connected component\)定义。

我们会注意到在有向图中，我们的得力助手palm树不见了...但是也并不是什么限制都没有的。在有向图中，除了树边、$frond$外，又新出现了一种$cross\-link$，即从某棵子树中的点指向另一棵子树中的某个点的边。除了上述这些边以外的边，如重边或是指向非子节点的后代节点的边，不会改变两点之间有没有路径的事实，所以我们丢弃。

我们很容易会发现任何$cross\-link$ $\(v,w\)$都具有着$NUMBER\(v\)\>NUMBER\(w\)$的性质（类似前面，我们现在在$DFS\(v,\-\)$中，所以如果$NUMBER\(v\)\<NUMBER\(w\)$，那么$w$一定是在$DFS\(v,\-\)$中被$NUMBER$的，即$w$是$v$的某个已经返回的后代节点，这是我们前述说的丢弃的边）。我们还可以有进一步的结论，在一串树边后的$cross\-link$指向某个"不在以起点为根的子树上"的点的$NUMBER$一定小于起点的$NUMBER$，方法是类似的，因为检查这条$cross\-link$时我们起点的$DFS$还没有返回，所以此时任何比它大的$NUMBER$都一定是后代节点，故而矛盾。

**引理10**: 图$G$中的点$v$和点$w$在同一个强连通分量中，$F$是$G$通过dfs得到的生成树森林。那么$v$和$w$在$F$中有一个公共的祖先。更进一步的，如果$u$是$v$和$w$的标号最大的祖先\(即lca\)，那么$u$和$v$以及$w$在同一个强连通分量中。

不妨令$v\< w$，$p$是$v$到$w$的路径，假设$T\_x$是在$F$中最小的包含$p$中所有点的根为$x$的子树。$T\_x$是一定存在的，因为$p$只可能从一棵子树前往一棵有着更小$NUMBER$的子树，而不可能前往一棵有着更大$NUMBER$的子树（这几个子树互不包含）。如果$p$包含$F$中两棵或更多的树的点，那么它不可能到达$w$，因为$v\< w$。
因此$T\_x$一定存在，进而$v$和$w$在$F$中有着公共的祖先节点。类似引理4中的证明，如果$x=v$或$x=w$那么显然。否则我们假设$T\_\{x\_\{1\}\}$和$T\_\{x\_\{2\}\}$是两棵包含了$p$上点的子树，并且$x\rightarrow x\_1$, $x\rightarrow x\_2$（即$x\_1$和$x\_2$是$x$的儿子）。如果只有一棵这样的子树，那么由于$T\_x$是最小的，会有$x$一定在$p$上（反证）；如果两棵这样的子树存在，因为$v\< w$，所以我们不妨假设$x\_1\<x\_2$，且$p$第一次离开$T\_\{x\_1\}$后第一次进入的其他子树是$T\_\{x\_2\}$，那么路径$p$如果想要离开$T\_\{u\_\{1\}\}$只能通过树边或是$frond$，因为$cross\-link$只能指向更小的$NUMBER$，它不能直接通过$cross\-link$进入$T\_\{x\_2\}$，那么$p$上必然有一条指向$x$的边。综上$v$和$w$的某个公共祖先节点$x$一定在$v$到$w$的路径$p$上，那么由于$w$到$v$还存在一条路径，那么$x$、$w$、$v$一定在同一个$SCC$中。
设$u$是$v$和$w$的lca，那么我们有$u$一定在$x\stackrel\{\*\}\{\rightarrow\} v$和$x\stackrel\{\*\}\{\rightarrow\} w$上，进而推出$u$一定和$x$、$v$、$w$在同一个$SCC$中。

**推论11**: $C$是$G$中的一个强连通分量，那么$C$中的点构成了$G$的生成树森林$F$中的某棵树的子树，这棵子树的根我们称之为强连通分量$C$的根。

这个推论和推论6一样，把找$SCC$转化为了找点是否是$SCC$的根。根其实就是dfs过程中某个$SCC$第一个被$NUMBER$的点，依次拿去不含其它根的子树，每次拿去的部分就是一个$SCC$。
说实话$LOWPT$的给出已经挺突兀的了，下面的$LOWLINK$的定义更是突兀，但是这引出了判断一个点是否是$SCC$根的方法。

$$
LOWLINK\(v\)=min\(\left\\\{v\right\\\}\cup\left\\\{w|v\stackrel\{\*\}\{\rightarrow\}\dashrightarrow w\\&\exists u\(u\stackrel\{\*\}\{\rightarrow\}v \\& u\stackrel\{\*\}\{\rightarrow\}w \\& u和w在G的同一个强连通分量中\)\right\\\}\)
$$

$LOWLINK\(v\)$就是与$v$在同一$SCC$的经0或更多条树边和至多一条$frond$或$cross\-link$可达的最小的点。说起来其实非常非常拗口，但是就是这样。

**引理12**: 我们计算出了有向图$G$中基于$G$的某个通过dfs得到生成树森林$F$的每个点的$LOWLINK$，则对于任一点$v$当且仅当$LOWLINK\(v\)=v$时，是$G$的某个强连通分量的根。

首先，如果$v$是根的话，那么$LOWLINK\(v\)=v$是显然的，因为如果$LOWLINK\(v\)\<v$的话，那么就说明$v$有一个真祖先节点才是根，这与$v$是根矛盾。
然后我们考虑另一方面，我们假定$u$是这个$SCC$的根，那么由于$u$和$v$在同一个$SCC$中，所以我们有$p:v\stackrel\{\*\}\{\Rightarrow\}u$，并且$v$在$T\_u$上，考虑$p$上第一条不指向$T\_v$上的点的边，我们设它指向的是$w$。这条边要么是$frond$，要么是$cross\-link$，但是无论它是什么我们都会有$w\<v$，进而有$LOWLINK\(v\)\leqq w\<v$，这与$LOWLINK\(v\)=v$矛盾。
综上，$LOWLINK\(v\)=v$和$v$是某个$SCC$的根是等价的。

**算法3**:
```
BEGIN
    INTEGER i;
    PROCEDURE STRONGCONNECT(v);
        BEGIN
            LOWLINK(v) := NUMBER(v) := i := i+1;
            put v on stack of points;
            FOR w in the adjacency list of v DO
                BEGIN
                    IF w is not yet numbered THEN
                        BEGIN COMMENT (v, w) is a tree arc;
                            STRONGCONNECT(w);
                            LOWLINK(v) := min(LOWLINK(v), LOWLINK(w));
                        END
                    ELSE IF NUMBER(w) < NUMBER(v) DO
                        BEGIN COMMENT (v, w) is a frond or cross-link;
                            if w is on stack of points THEN
                                LOWLINK(v) := min(LOWLINK(v), NUMBER(w));
                        END;
                    END;
                END;
            IF (LOWLINK(v) = NUMBER(v)) THEN
                BEGIN COMMENT v is the root of a component;
                    start new strongly connected component;
                    WHILE w on top of point stack satisfies
                          NUMBER(w) >= NUMBER(v) DO
                        delete w from point stack and put w in
                              current component
                END;
        END;
    i := 0;
    empty stack of points;
    FOR w a vertex IF w is not yet numbered THEN STRONGCONNECT(w);
END;
```

**定理13**: 该寻找强连通分量的算法需要$O\(V,E\)$的空间和时间。

该算法只是建立在dfs基础上做了一点额外操作。额外操作中每个点最多入栈出栈一次，检查是否在栈中可以用boolean数组来实现，因此会有与$V$和$E$有线性的关系的时间和空间。

**定理14**: 该寻找强连通分量的算法正确工作在任何有向图$G$中。

这里是我困惑的地方，也是我看着篇论文的最重要原因，但是我没有完全读明白。反复读后，我还是坚持觉得tarjan除了前面画错图外这里的证明也有点问题。所以我准备按照自己的理解来写，各位读者有兴趣的话可以去阅读[tarjan的论文](/pdf/tarjan.pdf)，引理14在13页的最下方。

因为判断$u$是否是某个$SCC$的根节点时，在$T\_u$上除了$u$以外的点已经全部返回了，所以如果有根的话，以它为根的子树已经全部出栈了。所以如果这个点是根节点的话，我们只管把栈中所有在根之后入栈的点和根出栈放入同一个$SCC$中即可。
那么判断我们的算法是否会正确运行，只需要知道判断根节点是否正确即可，即$LOWLINK$的计算是否正确。
我们还是用归纳法，用比较简单的说法来说，就是假设所有在点$v$结束$STRONGCONNECT$调用前已经结束调用的点的$LOWLINK$的计算都是正确的；用更直观的说法来说，就是所有$\left\\\{k|k\<v\\& k不是v的祖先节点\right\\\}\cup\left\\\{k|v\stackrel\{\*\}\{\rightarrow\} k\right\\\}$中的点的$LOWLINK$都正确计算。（tarjan的说法中没有除去祖先节点，但是事实上，祖先节点的$LOWLINK$还在计算中，它会正确计算，但是我们此时不知道它是否会正确计算，也不需要它正确计算）
首先我们看第一个返回的点，对于它来说最多只可能有$frond$来更新$LOWLINK$。如果它没有$frond$显然$LOWLINK$的值正确，否则设$\forall frond$ $v\dashrightarrow w$，显然$\exists u\(u=w\)$满足我们对$LOWLINK$更新的条件。
我们再看其他的点，假设在它前面返回的点都正确计算了$LOWLINK$，即所有$\left\\\{k|k\<v\\& k不是v的祖先节点\right\\\}\cup\left\\\{k|v\rightarrow k\right\\\}$中的点都正确计算了$LOWLINK$。
我们只需要关心$v\stackrel\{\*\}\{\rightarrow\} w\_1\dashrightarrow w\_2$，且$w\_2\<v$这样的路径，因为只有这样的路径才有可能更新$LOWLINK\(v\)$。（需要注意有可能存在$w\_1=v$）
假设$u$是$v$和$w\_2$的lca，那么有两种情况:
　　如果$u$和$w\_2$不在同一个$SCC$中，那么在$u\stackrel\{\*\}\{\rightarrow\}w\_2$上一定存在一个$SCC$的根并且那个点不是$u$，由于那个点会在$v$之前返回，所以它的$LOWLINK$一定会正确计算，那么$w\_2$会和它的根一起出栈，因此这种情况我们的算法不会更新$LOWLINK\(w\_1\)$和$LOWLINK\(v\)$（这种情况只会出现在$w\_1\dashrightarrow w\_2$是一条$cross\-link$的时候）。
　　如果$u$和$w\_2$在同一个$SCC$中，那么就满足了我们要求的条件，并且由于$u$还没有返回，根一定是$u$的祖先节点，所以$w\_2$一定没有出栈，我们会在$LOWLINK\(w\_1\)$中更新，进而会更新$LOWLINK\(v\)$。
综上我们的算法会正确计算$LOWLINK$，进而会正确判断根，进而会正确得出所有的$SCC$。

以上就是tarjan的论文的大多数内容了...说实话知道这种级别的dalao也会犯错误的时候，不知道为什么，还挺开心的（虽然读的时候非常非常非常痛苦...如某个图少一条边...）。

写这篇的主要目的是加深对tarjan算法的理解，看tarjan主要是因为之前看朱刘算法要用，但是我一次都没有真正弄明白过tarjan...所以就花了好几天看"没用"的"小"论文\(就结果来说，我想我一辈子都不会忘了tarjan是怎么回事了，虽然还是不能倒着写hhh

以后如果有合适的题，会贴上份代码什么的。

