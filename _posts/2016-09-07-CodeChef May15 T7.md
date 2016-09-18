---
layout: post
title:  "CodeChef May15 T7 SEZ 二分图匹配"
desc: "CodeChef May15 T7 SEZ Solution"
keywords: "二分图匹配"
date: 2016-09-07
categories: [problem]
tags: [二分图匹配]
icon: fa-bookmark-o
---



## 题目大意:

有一个$n$个点$m$条边的无向图，现从中选出$x$个不相邻点，令$y$为与这$x$个点相邻的点的个数。求最大化的$( x-y )$

<br>

## 题解:

现在在你面前的是一道涨姿势~~结论~~题。

我们要证明一下两个结论，然后套用二分图匹配解决这个问题。

先规定一些术语:

令$N(S)$表示所有与点集$S$中的点相邻的点的集合(注意$N(S)$可能包含集合$S$中的元素)。

$\mid =$表示满足

$[...]$是布尔表达式

$(u,v)$表示原图中一条$u $ 和$v$的边

$x_v$表示[$v \in S$]

$y_v$表示[$\exists u \in S ,\exists(u,v)$]

$\mid S \mid - \mid N(S) \mid=\sum_{v\in S}x_v - \sum_{v\in S} y_v$ (这里并没有强求$S$是一个独立集，下面会说为什么)

#### 结论1:

>  对于集合$S$，总存在一个独立集$S1$满足$\mid S \mid - \mid N(S) \mid \leq \mid S1 \mid - \mid N(S) \mid $

#### 证明1:

>  我们可以把集合$S$给切分成两个集合$A$和$B$，并且满足$(\forall v \in  A\ , \mid =[\ \nexists\  u\in A,\exists(u,v) ] )$,$(\forall v \in B, \mid =[\exists u\in A ,\exists (u,v)])$
>
>  不难发现$A$是一个独立集。
>
>  如果$A$是取出的集合，则
>
>  $\forall\  v \in A ,\mid=(x_v=1,y_v=0)$
>
>  $\forall\ v \in B,\mid = (x_v=1,y_v=1)$
>
>  $\forall\ v \notin A,v \notin B ,\mid = (x_v=0)$
>
>  注意，
>
>  $\sum_{v\in A} x_v -y_v +\sum_{v\in B} x_v -y_v=\sum_{v\in S} x_i -y_i$
>
>  但是对于那些不在集合$S$中的元素，$\sum_v x_v-y_v$会变大(因为取的点少了，与补集的边少了)
>
>  命题得证。

#### 用途1:

> 有了这个定理，我们在下面的考虑中可以忽略$S$是个独立集的约束。因为满足题设的最大的答案一定是独立集产生的。

#### 定理2:

> 在一个二分图$H=(L,R,E),\mid L\mid =\mid R \mid =n$中，$\max (\mid S \mid -\mid N(S)\mid )=n-k$，其中$k$是二分图的最大匹配。

#### 证明2:

> 先证明$\mid S \mid -\mid N(S) \mid \leq (n-k)$
>
> 左边的点集$L$有$(n-k)$个点没有被匹配。
> 那么我们考虑在右边的点集$R$里添加$n-k$个元素，然后我们把这$n-k$个点向$L$中的所有元素连边。现在，匹配数显然变成了$n$
>
> 然后有一个神奇的霍尔婚配定理([Hall's Marriage Theorem](https://en.wikipedia.org/wiki/Hall%27s_marriage_theorem)),是这么说的:
>
> > 在二分图$G=(S,T,E)$中，如果对于集合$S$的所有子集都满足与子集$S_i$相邻的点数$k_i \geq \mid S_i \mid$,则集合$S$满足匹配数$k \geq \mid S \mid$
>
> 这个感觉不难证，应该可以用数学归纳法，我就懒得证明了。。
>
> 然后这个应该是反过来也成立的。
>
> 那么我们观察构造出的新图，都满足$\mid S \mid \leq \mid N(S) \mid  +n-k$，然后移一下项，就可以得到目标式子。
>
> 然后我们再证明$\mid S \mid \geq n-k$
>
> 首先令$\max \mid S\mid - \mid N(S) \mid~ =x$
>
> 如果在点集$R$里添加$x$个点，那么就可以满足右边的点集$R$的所有子集$S$都满足霍尔婚配定理了(因为$x$是满足条件的最大值)
>
> 那么用全集$L$代掉$S$，可以得到$x+k \geq n \to x\geq n-k$
>
> 再由之前的式子可以推出$\max \mid S \mid - \mid N(S) \mid = n-k$

#### 用途2:

> 由用途1,在$\mid S \mid =\mid N(S) \mid$取到最大值时，$S$是独立集，所以可以直接由定理2得到答案。



然而蒟蒻不会二分图匹配，网络流瞎比来一发好了

哦对了上面全部引用的格式纯属为了好看。。不是真的引用。。若说由来是什么，感谢题解和ShinFeb的细心解释。

## Code

```cpp
#include<iostream>
#include<algorithm>
#include<string.h>
#include<stdio.h>
using namespace std;
const int M=805;
const int N=1605;
const int INF=1<<30;
struct Edge{
	int to,cap,nxt;
}G[N<<1];
int head[M],work[M],tot_edge,src,sink;
inline void add_edge(int from,int to,int c){
	G[tot_edge]=(Edge){to,c,head[from]};
	head[from]=tot_edge++;
	G[tot_edge]=(Edge){from,0,head[to]};
	head[to]=tot_edge++;
}
int que[M],d[M];
inline bool bfs(){
	int l=0,r=0;
	for(int i=0;i<=sink;++i)
		d[i]=-1;
	d[que[r++]=src]=0;
	for(;l<r;){
		int v=que[l++];
		for(int i=head[v];~i;i=G[i].nxt){
			int to=G[i].to;
			if(d[to]==-1&&G[i].cap)
				d[to]=d[v]+1,que[r++]=to;
		}
	}
	return d[sink]!=-1;
}
bool mark[M];
inline int dfs(int v,int f){
	if(v==sink)return f;
	mark[v]=1;
	for(int &i=work[v];~i;i=G[i].nxt){
		int to=G[i].to;
		if(!mark[to]&&d[to]==d[v]+1&&G[i].cap){
			int d=dfs(to,min(G[i].cap,f));
			if(d){
				G[i].cap-=d;
				G[i^1].cap+=d;
				return d;
			}
		}
	}
	return 0;
}
inline int Dinic(){
	int res=0;
	for(;bfs();){
		for(int i=0;i<=sink;++i)
			work[i]=head[i];
		for(;;){
			for(int i=0;i<=sink;++i)
				mark[i]=0;
			int f=dfs(src,INF);
			if(!f)break;
			res+=f;
		}
	}
	return res;
}
inline void rd(int &a){
	a=0;char c;
	while(c=getchar(),!isdigit(c));
	do a=a*10+(c^48);
		while(c=getchar(),isdigit(c));
}
int main(){
	memset(head,-1,sizeof(head));
	tot_edge=0;
	int n,m;cin>>n>>m;
	src=0,sink=n<<1|1;
	for(int i=1;i<=n;++i)
		add_edge(src,i,1),add_edge(i+n,sink,1);
	for(int i=1,a,b;i<=m;++i){
		rd(a),rd(b);
		add_edge(a,b+n,INF);
		add_edge(b,a+n,INF);
	}
	int res=Dinic();
	cout<<n-res<<endl;
	return 0;
}
```

