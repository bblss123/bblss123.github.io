---
layout: post
title:  "CodeChef March15 Task8TREECNT2 莫比乌斯反演+并查集"
desc: "CodeChef March15 Task8TREECNT2 Solution"
keywords: "莫比乌斯反演,并查集"
date: 2016-09-06
categories: [problem]
tags: [莫比乌斯反演,并查集]
icon: fa-bookmark-o
---



<h2>题目大意:</h2>

有一棵$n$个节点边权树，有$q$个操作，每次操作将第$A_i$条边的边权改为$C_i$。对于每次操作，你需要输出共有多少对$[S,T]$，满足从$S$到$T$的路径上所有边权值的$gcd$为$1$

<h2>题解:</h2>

先来一发无脑的反演。
<br>
$$\sum_S \sum_T \sum_{C_i \in E<S,T>} [gcd(C_i)=1]$$
<br>
$$=\sum_S \sum_T \sum_{C_i \in E<S,T> } \sum_d|gcd(C_i) \mu(d) $$
<br>
$$=\sum_S \sum_T \sum_{C_i \in E<S,T> } \sum_{d \mid C_1,d \mid C_2,... d \mid} \mu(d) $$
<br>

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

然而这题还卡常数。。更本卡不过去。。

搞不懂为什么标程for(;;)就可以踩线AC

看到PoPoQQQ也止步于27pt,感觉特别欣慰，跟上大牛的步伐一同弃坑。。



## Code

```cpp
#include<iostream>
#include<algorithm>
#include<stdio.h>
#include<string.h>
#include<vector>
#include<stack>
#include<queue>
#include<time.h>

using namespace std;
typedef long long ll;
typedef vector<int> vec;

#define pb push_back
#define ph push
#define bug(x) cout<<#x<<"="<<x<<" "
#define debug(x) cout<<#x<<"="<<x<<endl
#define showtime fprintf(stderr,"time = %.15f\n",clock() / (double)CLOCKS_PER_SEC)

template<class T>void rd(T &a){
	a=0;char c;
	while(c=getchar(),!isdigit(c));
	do a=a*10+(c^48);
		while(c=getchar(),isdigit(c));
}
template<class T>void nt(T x){
	if(!x)return;
	nt(x/10);
	putchar(48+x%10);
}
template<class T>void pt(T x){
	if(!x)putchar('0');
	else nt(x);
}
template<class T>void Max(T &a,T b){
	if(a<b)a=b;
}
template<class T>void Min(T &a,T b){
	if(a==-1||a>b)a=b;
}
const int M=1e6+5,N=1e5+5;//
int prime[M],mu[M],t;
bool is_prime[M];
inline void Euler(){
	mu[1]=1;
	for(int i=2;i<M;++i){
		if(!is_prime[i])prime[t++]=i,mu[i]=-1;
		for(int j=0,p;j<t&&1ll*(p=prime[j])*i<M;++j){
			is_prime[p*i]=1;
			if(i%p==0){mu[i*p]=0;break;}
			mu[i*p]=-mu[i];
		}
	}
}
int fac[100],atom[1000],s,sz[N],f[N];
ll res[105];
inline void facting(int x){
	int k=s=0;
	for(int i=0,p;i<t&&(p=prime[i])*p<=x;++i)
		if(x%p==0)for(fac[k++]=p;x%p==0;x/=p);
	if(x>1)fac[k++]=x;
	for(int S=1;S<1<<k;++S){
		int w=1;
		for(int i=0;i<k;++i)
			if(S&1<<i)w*=fac[i];
		atom[s++]=w;
	}
}
int stk[N],spr[N],stk2[N],par[N],sz2[N];
bool flag[N],mark[N],used[N],used2[N];
inline int find(int x){
	return x==f[x]?x:f[x]=find(f[x]);
}
inline int find_par(int x){
	return x==par[x]?x:par[x]=find_par(par[x]);
}
int A[N],B[N],C[N],a[N],c[N];
struct Edge{
	int to,nxt;
}G[M*20][2];
int head[M][2],tot[2];
inline void add_edge(int from,int to,bool f){
	G[tot[f]][f]=(Edge){to,head[from][f]};
	head[from][f]=tot[f]++;
}
int main(){
	freopen("data.in","r",stdin);
	freopen("ans.out","w",stdout);
	memset(head,-1,sizeof(head));
	memset(tot,0,sizeof(tot));
	int n,q,Mx=0;cin>>n;
	Euler();
	for(int i=1;i<n;++i){
		rd(A[i]),rd(B[i]),rd(C[i]);
		facting(C[i]);
		for(int j=0;j<s;++j)
			add_edge(atom[j],i,0);
		Max(Mx,C[i]);
	}
	cin>>q;
	for(int i=1;i<=q;++i){
		rd(a[i]),rd(c[i]),mark[a[i]]=1;
		facting(c[i]);
		for(int j=0;j<s;++j)
			add_edge(atom[j],i,1);
		Max(Mx,c[i]);
	}
	for(int i=1;i<=n;++i)
		f[i]=i,sz[i]=1;
	for(int i=0;i<=q;++i)
		res[i]=(ll)n*(n-1)>>1ll;
	ll add;
	for(int d=2,tp=0,sp=0,tp2=0;d<=Mx;++d){
		if(head[d][0]==-1&&head[d][1]==-1||mu[d]==0)continue;
		add=sp=0;
		for(int v=stk[tp];tp;v=stk[--tp])
			used[v]=0,sz[v]=1,f[v]=v;
		for(int v=stk2[tp2];tp2;v=stk2[--tp2])
			used2[v]=0;
		for(int i=head[d][0];~i;i=G[i][0].nxt){
			int to=G[i][0].to;
			int l=A[to],r=B[to];
			if(!used[l])used[l]=1,stk[++tp]=l;
			if(!used[r])used[r]=1,stk[++tp]=r;
			if(mark[to])spr[++sp]=to;
			else{
				int f1=find(l),f2=find(r);
				if(f1==f2)continue;
				add+=(ll)sz[f1]*sz[f2];
				f[f2]=f1,sz[f1]+=sz[f2];
			}
		}
		for(int i=head[d][1];~i;i=G[i][1].nxt){
			int id=a[G[i][1].to];
			int l=A[id],r=B[id];
			int f1=find(l),f2=find(r);
			if(f1==f2)continue;
			if(!used2[f1])used2[f1]=1,stk2[++tp2]=f1;
			if(!used2[f2])used2[f2]=1,stk2[++tp2]=f2;
		}
		for(int i=1;i<=sp;++i){
			int f1=find(A[spr[i]]),f2=find(B[spr[i]]);
			if(!used2[f1])used2[f1]=1,stk2[++tp2]=f1;
			if(!used2[f2])used2[f2]=1,stk2[++tp2]=f2;
		}
		for(int i=0;i<=q;++i){
			ll cur=add;
			for(int j=1;j<=tp2;++j)
				par[stk2[j]]=f[stk2[j]],sz2[stk2[j]]=sz[stk2[j]];
			for(int j=1;j<=sp;++j){
				if(spr[j]==a[i])spr[j]=0;
				if(spr[j]){
					int f1=find_par(find(A[spr[j]]));
					int f2=find_par(find(B[spr[j]]));
					if(f1==f2)continue;
					cur+=(ll)sz2[f1]*sz2[f2];
					par[f2]=f1,sz2[f1]+=sz2[f2];
				}
			}
			for(int j=i;j>=1;--j){
				if(flag[a[j]])continue;
				flag[a[j]]=1;
				if(c[j]%d==0){
					flag[a[j]]=1;
					int f1=find_par(find(A[a[j]]));
					int f2=find_par(find(B[a[j]]));
					if(f1==f2)continue;
					cur+=(ll)sz2[f1]*sz2[f2];
					par[f2]=f1,sz2[f1]+=sz2[f2];
				}
			}
			for(int j=i;j>=1;--j)
				flag[a[j]]=0;
			res[i]+=mu[d]*cur;
		}
	}
	for(int i=0;i<=q;++i)
		printf("%lld\n",res[i]);
	return 0;
}

```

