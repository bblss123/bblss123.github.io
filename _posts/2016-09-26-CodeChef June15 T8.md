---
layout: post
title:  "CodeChef June15 MOREFB FFT+分治"
desc: "CodeChef June15 MOREFB Solution"
keywords: "FFT,分治"
date: 2016-09-26
categories: [problem]
tags: [FFT,分治]
icon: fa-bookmark-o
---

## 题目大意:

定义$fib(x)$表示前两项为$1$的斐波那契数列第$x$项

给出一个大小为$n$的集合$S$以及一个整数$k$，求$\sum_{s \subseteq S,\mid s\mid =k} fib(sum(s))$

<br>

## 题解:

$10$年了，我终于会推斐波那契数列通项了！

>$x_{n+2}=x_{n+1}+x_n$
>
>$x_{n+2}-r x_{n+1}=s(x_{n+1}-rx_n)$
>
>$\begin{cases} s+r=1 \\\ -sr=1 \end{cases}$
>
>展开一开始的式子，迭代可得:$fib(n)=s^{n-1}+r\times fib(n-1)$
>
>再迭代这个式子，得到:$fib(n)=\frac{s^n-r^n}{s-r}$
>
>由之前的方程可得$fib(n)=\frac{\sqrt{5}}{5}[(\frac{1+\sqrt{5}}{2})^n-(\frac{1-\sqrt{5}}{2})^n]$

那个在模$P$的情况下的根号处理以及除法大家都烂熟于心了，就不提了吧

所以我们化简题目的式子:

$$\sum_{s \subseteq S,\mid s\mid =k} fib(sum(s))$$

令$ca=\frac{1+\sqrt{5}}{2},cb=\frac{1-\sqrt{5}}{2},cst=\frac{\sqrt{5}}{5}$

$$=\sum_{s\subseteq S,\mid s\mid=k}cst\times[ca^{sum(x)}-cb^{sum(x)}]$$

也就是说

定义$dp[x]$:

$$dp[x]=\sum_{s\subseteq T,\mid s\mid =x} fib(sum(x))$$

我们可以把集合进行分治

假设集合$S$被分成了$S1$和$S2$，左边的$dp$用$f$表示，右边的$dp$用$g$表示

那么整个区间的$dp$值可以这样计算:

$dp[i]=\sum_{j=1}^i f[j]g[i-j]$

这是一个裸的卷积，$FFT$优化转移即可

这题TM卡double，出题人丧尽天良。。

<br>

## Code

```cpp
#include<iostream>
#include<string.h>
#include<algorithm>
#include<stdio.h>
#include<complex>
#include<math.h>
using namespace std;
const int M=1e5+5;
const int sq5=10104;
const int cst=22019;
const int ca=55048;
const int cb=44944;
const int P=99991;
typedef long double db;
typedef long long ll;
typedef complex<db> comp;
const db twpi=3.14159265358979*2;
inline int Mod_Pow(int x,int a){
	int res=1;
	for(;a;a>>=1,x=(ll)x*x%P)
		if(a&1)res=(ll)res*x%P;
	return res;
}
#define bug(x) cout<<#x<<"="<<x<<" "
#define debug(x) cout<<#x<<"="<<x<<endl
int n,k,rev[M<<2];
inline void DFT(comp *res,int n,int sg){
	for(int i=1;i<n;++i)
		if(rev[i]>i)swap(res[i],res[rev[i]]);
	for(int s=2;s<=n;s<<=1){
		comp wm(cos(twpi/s),sin(twpi/s)*sg);
		for(int i=0;i<n;i+=s){
			comp w(1,0);
			for(int j=0;j<s>>1;++j,w*=wm){
				comp a=res[i+j],b=res[i+j+(s>>1)];
				res[i+j]=a+b*w,res[i+j+(s>>1)]=a-b*w;
			}
		}
	}
	if(sg<0)for(int i=0;i<n;++i)
			res[i]/=n;
}
comp A[M<<2],B[M<<2],C[M<<2];
#include<vector>
typedef vector<int> vec;
#define pb push_back
inline vec FFT(vec &f,vec &g){
	vec res;res.clear();
	int n=f.size(),m=g.size(),w=1,s=0;
	for(;w<n+m;w<<=1,++s);
	for(int i=0;i<n;++i)A[i]=f[i];
	for(int j=0;j<m;++j)B[j]=g[j];
	for(int i=n;i<w;++i)A[i]=0;
	for(int j=m;j<w;++j)B[j]=0;
	for(int i=1;i<w;++i)
		rev[i]=rev[i>>1]>>1|(i&1)<<s-1;
	DFT(A,w,1),DFT(B,w,1);
	for(int i=0;i<w;++i)
		C[i]=A[i]*B[i];
	DFT(C,w,-1);
	for(int i=0;i<n+m-1;++i)
		res.pb((ll){C[i].real()+0.5}%P);
	return res;
}
int sgn;
inline int fib(int x){
	if(sgn)return Mod_Pow(ca,x);
	return Mod_Pow(cb,x);
}
int num[M];
vec tmp;
inline vec solve(int l,int r){
	if(l==r){
		tmp.clear(),tmp.pb(1),tmp.pb(fib(num[l]));
		return tmp;
	}
	int mid=l+r>>1;
	vec f=solve(l,mid),g=solve(mid+1,r);
	return FFT(f,g);
}
inline void rd(int &a){
	a=0;char c;
	while(c=getchar(),!isdigit(c));
	do a=a*10+(c^48);
		while(c=getchar(),isdigit(c));
}
inline int Mod(int x){
	if(x>=P)return x-P;
	if(x<0)return x+P;
	return x;
}
int main(){
	cin>>n>>k;
	for(int i=1;i<=n;++i)
		rd(num[i]);
	sgn=1;vec f=solve(1,n);
	sgn=0;vec g=solve(1,n);
	int res=Mod(f[k]-g[k])*(ll)cst%P;
	cout<<res<<endl;
	return 0;
}
```

