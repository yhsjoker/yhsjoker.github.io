---
title: 无聊的数列
date: 2022-12-03 14:20:13
tags:
    - [洛谷]
    - [线段树]
    - [蓝]
categories: 题解
description: 一道裸的线段树,需要考虑对区间修改的方式如何进行转化
---

## [原题戳这里](https://www.luogu.com.cn/problem/P1438)

## 题目描述

维护一个数列a_i,支持两种操作:

+ `1 l r K D`:给出一个长度等于r-l+1的等差数列,首项为K,公差为D,并将它对应加到[l,r]范围内的每一个数上,即:令a_l=a_l+K,a_{l+1}=a_{l+1}+K+D...a_r=a_r+K+(r-l)*D

+ `2 p`:询问序列的第p个数的值a_p

## 输入格式

第一行两个整数数n,m表示数列长度和操作个数

第二行n个整数,第i个数表示a_i

接下来的m行,每行先输入一个整数 opt

若opt=1则再输入四个整数l,r,K,D

若opt=2则再输入一个整数p

## 输出格式

对于每个询问，一行一个整数表示答案

## 输入输出样例

### 输入 #1
```
5 2
1 2 3 4 5
1 2 4 1 2
2 3
```
### 输出 #1
```
6
```

## 说明/提示

### 数据规模与约定

对于100%数据,0<=n,m<=100000,-200<=a_i,K,D<=200,1<=l<=r<=n,1<=p<=n

---

## 题目大意

维护一个能实现上述操作的序列

## 思路

由于需要维护区间操作信息,所以我们首先想到线段树,因此我们需要思考如何用线段树维护增加等差数列的信息.暴力单点修改每一个点很明显是会超时的,如果用懒标记直接维护每个区间的起始项,公差也很难pushdown下去,因此我们需要转换思路.

等差数列有个很重要的性质就是差相等,因此如果用差分来做的话就可以将区间增加等差数列转换为区间增加相同的数,很容易用懒标记来维护.因此我们线段树中维护的就应该是原序列的差分序列,增加等差数列的操作就变成了区间每个数增加相同的数,单点查询就变成了查询区间和,这些操作很容易实现.

举个例子:

原序列: 0,0,0,0,0,0,0

原序列的差分序列: 0,0,0,0,0,0,0

为原序列2到5的位置增加一个等差序列2,3,4,5

原序列变为: 0,2,3,4,5.0,0

差分序列变为0,2,1,1,1,-5,0

## 代码
```cpp
#include<iostream>
#include<algorithm>
#include<cstring>
#define int long long
#define IOS ios::sync_with_stdio(0);\
cin.tie(0);\
cout.tie(0);

using namespace std;

const int N=100010;

struct Node
{
	int l,r;
	int sum,add;
};

Node tr[N*4];
int w[N];

void pushup(Node &u,Node &l,Node &r)
{
	u.sum=l.sum+r.sum;
}

void pushup(int u)
{
	pushup(tr[u],tr[u<<1],tr[u<<1|1]);
}

void build(int u,int l,int r)
{
	if(l==r)
		tr[u]={l,r,w[r]-w[r-1],0};
	else
	{
		tr[u]={l,r};
		int mid=(l+r)/2;
		build(u<<1,l,mid);
		build(u<<1|1,mid+1,r);
		pushup(u);
	}
}

void pushdown(Node &u,Node &l,Node &r)
{
	if(u.add)
	{
		l.add+=u.add;
		r.add+=u.add;
		l.sum+=(l.r-l.l+1)*u.add;
		r.sum+=(r.r-r.l+1)*u.add;
		u.add=0;
	}
}

void pushdown(int u)
{
	pushdown(tr[u],tr[u<<1],tr[u<<1|1]);
}

void modify(int u,int l,int r,int k)
{
	if(l<=tr[u].l&&tr[u].r<=r)
	{
		tr[u].sum+=(tr[u].r-tr[u].l+1)*k;
		tr[u].add+=k;
	}
	else
	{
		pushdown(u);
		int mid=(tr[u].l+tr[u].r)/2;
		if(l<=mid) modify(u<<1,l,r,k);
		if(r>mid) modify(u<<1|1,l,r,k);
		pushup(u);
	}
}

int query(int u,int l,int r)
{
	if(l<=tr[u].l&&tr[u].r<=r)
		return tr[u].sum;
	else
	{
		pushdown(u);
		int mid=(tr[u].l+tr[u].r)/2;
		int res=0;
		if(l<=mid) res+=query(u<<1,l,r);
		if(r>mid) res+=query(u<<1|1,l,r);
		return res;
	}
}

signed main()
{
	IOS
	int n,m;
	cin>>n>>m;
	for(int i=1;i<=n;i++)
		cin>>w[i];
	
	build(1,1,n);
	
	while(m--)
	{
		int op,l,r,k,d,p;
		cin>>op;
		if(op==1)
		{
			cin>>l>>r>>k>>d;

			modify(1,l,l,k);

			if(l+1<=n)modify(1,l+1,r,d);
                //注意要防止越界

			if(r+1<=n)modify(1,r+1,r+1,-(k+(r-l)*d));
                //记得差分序列要在末尾还原之前造成的影响
		}
		else
		{
			cin>>p;
			cout<<query(1,1,p)<<'\n';
		}
	}
}
```