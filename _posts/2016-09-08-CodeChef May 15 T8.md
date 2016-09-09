---
layout: post
title:  "CodeChef May15 T8 CBAL 压位+分块"
desc: "CodeChef May15 T8 CBAL Solution"
keywords: "压位,分块"
date: 2016-09-07
categories: [problem]
tags: [blog]
icon: fa-bookmark-o
---



## 题目大意:

定义“平衡字符串”为每种字符都出现偶数次的字符串。
有一个只包含小写字母的字符串$S$(长度为$n$)和$q$个询问。
询问$(L,R,type)$：输出$S$的所有左右端点在$[L, R]$内的平衡子串的长度的$type$次方之和。$type\in \lbrace 0, 1, 2\rbrace$
强制在线。

$n \leq 10^5,q \leq 10^5 $

## 题解:

先把问题简化了吧。

奇偶性有异或的性质，所以我们可以用一个二进制$\lbrace A \rbrace$表示每个元素出现的状态。如果一个串每个元素都出现了偶数次，则这个串开头前的二进制值和这个串结尾的二进制相同。

那么问题可以简化成对于每个询问，求$\sum_{s=L-1}^R \sum_{t=s+1}^R [A[s]=A[t]]*(r-s)^{type}$

然后这个二进制还有个很好的性质：可以离散。

那么我们可以分块预处理每个区间$A_i$出现的次数。

现在我们看一下每个询问那些区间对答案的有什么样的贡献。

<img src="/20160909分块.png" width = "100" alt="由于比较懒直接扒分块大师pdf里的图片" align=center />
