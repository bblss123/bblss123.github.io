---
layout: post
title:  "CodeChef May15 T8 CBAL 压位+分块"
desc: "CodeChef May15 T8 CBAL Solution"
keywords: "压位,分块"
date: 2016-09-07
categories: [problem]
tags: [压位,分块]
icon: fa-bookmark-o
---



## 题目大意:

定义“平衡字符串”为每种字符都出现偶数次的字符串。
有一个只包含小写字母的字符串$S$(长度为$n$)和$q$个询问。
询问$(L,R,type)$：输出$S$的所有左右端点在$[L, R]$内的平衡子串的长度的$type$次方之和。$type\in \lbrace 0, 1, 2\rbrace$
强制在线。

$n \leq 10^5,q \leq 10^5 $

<br>

## 题解:

先把问题简化了吧。

奇偶性有异或的性质，所以我们可以用一个二进制$\lbrace A \rbrace$表示每个元素出现的状态。如果一个串每个元素都出现了偶数次，则这个串开头前的二进制值和这个串结尾的二进制相同。

那么问题可以简化成对于每个询问，求$\sum_{s=L-1}^R \sum_{t=s+1}^R [A[s]=A[t]]*(r-s)^{type}$

然后这个二进制还有个很好的性质：可以离散。

那么我们可以分块预处理每个区间$A_i$出现的次数。

现在我们看一下每个询问那些区间对答案的有什么样的贡献。

不妨设当前询问$[L,R]$包裹着的最大块为$[s,e]$

答案由$[s,R]+[L,e]-[s,e]$+左端点在$[L,s]$,右端点在$[e,R]$中的元素构成。

也就是说分块预处理一下前面那些东西然后暴力算后面那部分即可。

当然如果在一个块里暴力大家都会的。

本题细节较多不另叙述，详情请见代码。。

<br>

## Code

```cpp
#include<iostream>
#include<algorithm>
#include<string.h>
#include<stdio.h>
#include<math.h>
#include<assert.h>
using namespace std;
typedef long long ll;
#define bug(x) cout<<#x<<"="<<x<<" "
#define debug(x) cout<<#x<<"="<<x<<endl
const int M=1e5+5;
int A[M],B[M],S,cnt[M],n,s;
ll les0[320][M],les1[320][M],les2[320][M],res0[320][M],res1[320][M],res2[320][M],sum[M],pw[M];
char str[M];
template<class T>void rd(T &a){
	a=0;char c;
	while(c=getchar(),!isdigit(c));
	do a=a*10+(c^48);
		while(c=getchar(),isdigit(c));
}
inline ll Pow(int x){return 1ll*x*x;}
inline void pret(){
	for(int i=0;i*S<=n;++i){
		for(int j=i*S;j<=n;++j){
			if(j)les0[i][j]=les0[i][j-1]+cnt[A[j]];
			if(j)les1[i][j]=les1[i][j-1]+(ll)cnt[A[j]]*j-sum[A[j]];
			if(j)les2[i][j]=les2[i][j-1]+pw[A[j]]+cnt[A[j]]*Pow(j)-2*sum[A[j]]*j;
			sum[A[j]]+=j,++cnt[A[j]],pw[A[j]]+=Pow(j);
		}
		for(int j=0;j<=s;++j)
			cnt[j]=sum[j]=pw[j]=0;
	}
	A[n+1]=0;
	for(int i=n/S+1;i;--i){
		int w=i*S>n?n+1:i*S;
		for(int j=w-1;j>=0;--j){
			res0[i][j]=res0[i][j+1]+cnt[A[j]];
			res1[i][j]=res1[i][j+1]-(ll)cnt[A[j]]*j+sum[A[j]];
			res2[i][j]=res2[i][j+1]+pw[A[j]]+cnt[A[j]]*Pow(j)-2*sum[A[j]]*j;
			sum[A[j]]+=j,++cnt[A[j]],pw[A[j]]+=Pow(j);
		}
		for(int j=0;j<=s;++j)
			cnt[j]=sum[j]=pw[j]=0;
	}
}
template<class T>void nt(T x){
	if(!x)return;
	nt(x/10),putchar(x%10+48);
}
template<class T>void pt(T x){
	if(!x)putchar('0');
	else nt(x);
}
ll tmp[M],tp[M],tpw[M];
inline void clr(int l,int r){
	for(int i=l;i<r;++i)
		tmp[A[i]]=tp[A[i]]=tpw[A[i]]=0;
}
int solve0(int l,int r){
	int tl=l/S+1,tr=r/S;
	if(tl==tr+1){
		ll ans=0;
		for(int i=l;i<=r;++i)
			ans+=tmp[A[i]],++tmp[A[i]];
		clr(l,r+1);
		return ans;
	}
	ll ans=les0[tl][r]+res0[tr][l]-les0[tl][tr*S-1];
	for(int i=l;i<tl*S;++i)
		++tmp[A[i]];
	for(int i=tr*S;i<=r;++i)
		ans+=tmp[A[i]];
	clr(l,tl*S);
	return ans;
}
ll solve1(int l,int r){
	int tl=l/S+1,tr=r/S;
	if(tl==tr+1){
		ll ans=0;
		for(int i=l;i<=r;++i){
			ans+=(ll)tmp[A[i]]*i-tp[A[i]];
			tp[A[i]]+=i,++tmp[A[i]];
		}
		clr(l,r+1);
		return ans;
	}
	ll ans=les1[tl][r]+res1[tr][l]-les1[tl][tr*S-1];
	for(int i=l;i<tl*S;++i)
		++tmp[A[i]],tp[A[i]]+=i;
	for(int i=tr*S;i<=r;++i)
		ans+=(ll)i*tmp[A[i]]-tp[A[i]];
	clr(l,tl*S);
	return ans;
}
ll solve2(int l,int r){
	int tl=l/S+1,tr=r/S;
	if(tl==tr+1){
		ll ans=0;
		for(int i=l;i<=r;++i){
			ans+=tpw[A[i]]+tmp[A[i]]*Pow(i)-2*tp[A[i]]*i;
			tp[A[i]]+=i,++tmp[A[i]],tpw[A[i]]+=Pow(i);
		}
		clr(l,r+1);
		return ans;
	}
	ll ans=les2[tl][r]+res2[tr][l]-les2[tl][tr*S-1];
	for(int i=l;i<tl*S;++i)
		++tmp[A[i]],tp[A[i]]+=i,tpw[A[i]]+=Pow(i);
	for(int i=tr*S;i<=r;++i)
		ans+=tpw[A[i]]+tmp[A[i]]*Pow(i)-2*tp[A[i]]*i;
	clr(l,tl*S);
	return ans;
}
inline void gao(){
	scanf("%s",str+1);
	n=strlen(str+1);
	for(int i=1;i<=n;++i)
		str[i]-='a';
	for(int i=1;i<=n;++i)
		A[i]=A[i-1]^(1<<str[i]),B[i]=A[i];
	B[0]=0;
	sort(B,B+n+1);
	s=unique(B,B+n+1)-B;
	for(int i=1;i<=n;++i)
		A[i]=lower_bound(B,B+s,A[i])-B;
	S=(int)sqrt(n+1);
	pret();
	int q;cin>>q;
	ll _A=0,_B=0;
	for(int X,Y,type;q--;){
		rd(X),rd(Y),rd(type);
		int L=(X+_A)%n+1;
		int R=(Y+_B)%n+1;
		if(L>R)swap(L,R);
		ll ans;--L;
		if(type==0)ans=solve0(L,R);
		if(type==1)ans=solve1(L,R);
		if(type==2)ans=solve2(L,R);
		_A=_B,_B=ans%n;
		pt(ans),putchar('\n');
	}
	for(int i=0;(i-1)*S<=n;++i)
		for(int j=0;j<=n+1;++j){
			les0[i][j]=les1[i][j]=les2[i][j]=0;
			res0[i][j]=res1[i][j]=res2[i][j]=0;
		}
}
int main(){
	int _;
	for(cin>>_;_--;)gao();
	return 0;
}
```

