---
layout: post
title:  "CodeChef March15 Task8TREECNT2 莫比乌斯反演+并查集"
desc: "CodeChef March15 Task8TREECNT2 Solution"
keywords: "莫比乌斯反演,并查集"
date: 2016-09-06
categories: [problem]
tags: [blog]
icon: fa-bookmark-o
---



##题目大意:

有一棵$n$个节点边权树，有$q$个操作，每次操作将第$A_i$条边的边权改为$C_i$。对于每次操作，你需要输出共有多少对$[S,T]$，满足从$S$到$T$的路径上所有边权值的$gcd$为$1$

##题解:

先来一发无脑的反演。
$$\sum_S \sum_T \sum_{C_i \in E<S,T>} [gcd(C_i)=1]$$
$$=\sum_S \sum_T \sum_{C_i \in E<S,T> } \sum_d|gcd(C_i) \mu(d) $$
$$=\sum_S \sum_T \sum_{C_i \in E<S,T> } \sum_{d \mid C_1,d \mid C_2,... d \mid} \mu(d) $$

那么我们只需枚举$d$，把可以被$d$整除的边的都给连起来，数一下每个联通块的点对，最后再乘上$\mu(d)$即可。

但是关键在于如何在有修改的情况下处理这些内容。

首先对于那些不会更改的边，乘早处理掉。

然后去看那些被更改的边，可以用$O(q^2)$的复杂度处理出对询问的贡献吧

这里即有删边的操作，又有加边的操作。

现在的问题是，本来某条边属于未更改的集合，然后在某次更改中，它不在可以被$d$整除。但是很不幸，在接下来的更改中，它又被改回来了，然后，它又被删除了......也就是在一个区间上这条边被反复异或。

orz Stilwell选择了使用CDQ分治解决这一问题。

但其实这里没有必要强求$O(q\log(q))$的复杂度。

于是标程给了一个神(bao)奇(li)的做法。。

对于每个询问，可以知道此刻到底都有哪些玩意变化了。然后把可能变化的信息都给拷出来，重新构图，暴力计算。

这个做法也是让我深感奥(yi)妙(lian)重(meng)重(bi)

那么这题就被这种奇(meng)妙(bi)的方法给解(shui)掉了。。



## Code

```cpp

```

