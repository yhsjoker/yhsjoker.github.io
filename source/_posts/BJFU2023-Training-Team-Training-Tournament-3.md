---
title: BJFU-2023-集训队训练赛-3
date: 2023-01-13 10:42:49
tags:
    - [集训队训练赛]
categories: 题解
description: BJFU集训队寒假训练题目（部分）
---

## A

### 题目链接
https://codeforces.com/problemset/problem/1611/C

### 题目大意
给定一个变化后的序列b，求变化前的序列a，打印任意一种即可，a中元素互不相同，且范围为1-n，变化规则为：
+ 如果数列a最左端的元素小于最右端的元素，则将a的最左端元素放入数列b的最左端，如果数列a最右端的元素小于最左端的，则将最右端元素放入数列b的最右端

### 思路
首先我们可以得知，n一定是最后进入序列b的，因此n一定出现在b的最左端或最右端，若未出现的话，则不可能变化成功。我们可以采用倒推，如果b的首部元素小于尾部，则将首部元素放回a的左端，若b的尾部元素小于首部，则将尾部元素放回a的右端，复原回去的a就满足条件。

```cpp
int n;
list<int> a,ans;

void init()
{
	a.clear();
	ans.clear();
	cin>>n;
	for(int i=0;i<n;i++)
	{
		int x;
		cin>>x;
		a.push_back(x);
	}
}

void solve()
{
	init();
	if(a.front()==n||a.back()==n)
	{
		while(!a.empty())
		{
			if(a.front()<a.back())
			{
				ans.push_front(a.front());
				a.pop_front();
			}
			else
			{
				ans.push_back(a.back());
				a.pop_back();
			}
		}
		for(auto e:ans)
		{
			cout<<e<<' ';
		}
		cout<<'\n';
	}
	else
	{
		cout<<"-1\n";
	}
}
```

## B

### 题目链接
https://codeforces.com/problemset/problem/1553/B

### 题目大意
给定两个字符串s1，s2，问s2能否通过下列规则得出
+ 在s1中选择一个位置pos，将其向右滑动k1，再向左滑动k2，滑过的所有字符依次组成s2，其中k1，k2>=0，如s1="abc"，s2可以为"abcba"

### 思路
定义一个字符串为abcdefg，则在d点掉头的字符串一定可以在如下字符串中找到，abcdcba，因此我们可以将所有s1能产生的字符串记录，然后查询字符串中是否含有s2即可

### 代码
```cpp
string s1,s2;
vector<string> v;

void init()
{
	cin>>s1>>s2;
	v.clear();
}

void solve()
{
	init();
	for(int i=1;i<=s1.size();i++)
	{
		string rs=s1.substr(0,i-1);
		reverse(rs.begin(),rs.end());
		v.push_back(s1.substr(0,i)+rs);
	}
	for(const auto& e:v)
	{
		if(e.find(s2)!=string::npos)
		{
			cout<<"YES\n";
			return;
		}
	}
	cout<<"NO\n";
}
```

## C

### 题目链接
https://codeforces.com/problemset/problem/1491/D

### 题目大意
给全体自然数按照一下规则连有向边，问是否可以从a走到b，连边规则为：
+ u->u+v,其中u&v==v

### 思路
通过模拟我们发现，u+v的二进制中含有的1的数量小于等于u，并且u+v在任意二进制位中u的后缀1数量都要大于等于u+v，这一点显然是可以递推的，因此，答案就是判断a和b是否满足上述关系即可。

### 代码
```cpp
int a,b;

void input()
{
	cin>>a>>b;
}

void solve()
{
	input();
	if(a>b){cout<<"NO\n";return;}
	int cnta=0,cntb=0;
	for(int i=0;i<=31;i++)
	{
		if(a>>i&1) cnta++;
		if(b>>i&1) cntb++;
		if(cnta<cntb)
		{
			cout<<"NO\n";
			return;
		}
	}
	cout<<"YES\n";
}
```

## D

### 题目链接
https://codeforces.com/problemset/problem/1490/C

### 题目大意
给定一个数x，问是否能分解为a^3+b^3的形式，其中a,b>0

### 思路
假定a>=b，枚举a，如果a^3>=x或者2*a^3<x，则都无法成立，否则，判断x-a^3是否为某一整数的三次方即可

### 代码
```cpp
int x;

void init()
{
	cin>>x;
}

void solve()
{
	init();
	for(int i=1;i<=10000;i++)
	{
		int i3=i*i*i;
		if(i3>=x){cout<<"NO\n";return;}
		if(i3*2<x) continue;
		int j3=x-i3;
		int j=round(pow(j3,1.0/3));
		if(j*j*j==j3)
		{
			cout<<"YES\n";
			return;
		}
	}
}
```

## E

### 题目链接
https://codeforces.com/problemset/problem/1486/B

### 题目大意
给定一组二维点，求到所有点的哈密尔顿距离之和最短的点的个数

### 思路
首先判断一维点中哈密尔顿距离最短的情况。如果是奇数个点，则一定位于最中间的点，如a1,a2,a3,a4,a5，在a1至a5范围内距离都是a5-a1，在a2至a4范围内距离都是a4-a2，则想要使总距离最短，则此点必须与中心点距离最短，即0。若是偶数个点，则中间两个点的闭区间内距离都为最短，证明方法同上。二维中距离和最短的点的个数就是求出横坐标集和纵坐标集中距离最短的点的个数的乘积。

### 代码
```cpp
int n;
int x[N],y[N];

void init()
{
	cin>>n;
	for(int i=0;i<n;i++)
		cin>>x[i]>>y[i];
}

void solve()
{
	init();
	sort(x,x+n);
	sort(y,y+n);
	if(n&1)
	{
		cout<<1<<'\n';
	}
	else
	{
		int nx=x[n/2]-x[n/2-1]+1;
		int ny=y[n/2]-y[n/2-1]+1;
		cout<<nx*ny<<'\n';
	}
}
```

## F

### 题目链接
https://codeforces.com/problemset/problem/1482/B

### 题目大意
给定一个序列，求最大的m，序列满足规则如下：
+ a[1]=s%m,a[i]=(a[i-1]+c)%m
+ n>0,m>0,c>=0且c<m,s>=0

### 思路
根据规则可以得出c=(a[i+1]-a[i]+m)%m。首先用a[i+1]-a[i]，如果计算出c有两组以上的解，则不存在m。如果计算出两组解，若同为正或同为负，则也不存在，若一正一负，则可以得出m就是它们的差值。若只有一组解，则m可以取无穷大都满足。

### 代码
```cpp
int n;
int a[N];
set<int> s;

void init()
{
	cin>>n;
	for(int i=0;i<n;i++)
		cin>>a[i];
	s.clear();
}

void solve()
{
	init();
	for(int i=1;i<n;i++)
		s.insert(a[i]-a[i-1]);
	if(s.size()>2)
		cout<<"-1\n";
	else if(s.size()==2)
	{
		int x=*s.begin(),y=*(++s.begin());
		if(x*y>=0) cout<<"-1\n";
		else
		{
			int m=0;
			if(x>y) m=x-y;
			else m=y-x;
			for(int i=0;i<n;i++)
				if(m<=abs(a[i]))
				{
					cout<<"-1\n";
					return;
				}
			cout<<m<<' '<<(a[1]-a[0]+m)%m<<'\n';
		}
	}
	else
	{
		cout<<"0\n";
	}
}
```

## G

### 题目链接
https://codeforces.com/problemset/problem/1478/B

### 题目大意
给定n个整数和一个d，判断每一个整数能否分解为任意个含d的数。

### 思路
当整数a大于d\*10后，一定可以取出来，因为一定能满足以下的分解形式，其中一个数是d\*10+(a-d\*10)%d,另外的所有数都是d。因此只需要开始处理出小于d*10的数能否成功分解即可。

### 代码
```cpp
set<int> s[10];
int q,d;

void dfs(int k,int sum)
{
	for(int i=k;i<k*10;i+=10)
	{
		int su=sum+i;
		if(su>=k*10) continue;
		if(s[k].count(su)) continue;
		s[k].insert(su);
		dfs(k,su);
	}
}

void init()
{
	for(int i=1;i<=9;i++)
		dfs(i,0);
}

void solve()
{
	cin>>q>>d;
	while(q--)
	{
		int a;
		cin>>a;
		if(a>=d*10) cout<<"YES\n";
		else
		{
			if(s[d].count(a)) cout<<"YES\n";
			else cout<<"NO\n";
		}
	}
}
```

## H

### 题目链接
https://codeforces.com/problemset/problem/1461/B

### 题目大意
问给定图形中含有多少个指定图形

### 思路
我们可以发现，一个更大的指定图形可以由四个小的图形组成，因此可以考虑动态规划。设f[i][j]点表示指定图形在该点的最大边长，其中以指定图形的最中下部为基准，向单侧延伸的长度为边长。那么若f[i-1][j],f[i][j],f[i][j-1],f[i][j+1]中的值都大于等于x，则f[i][j]点更新为x+1

### 代码
```cpp
int n,m;
char a[N][N];
int f[N][N];

void init()
{
	cin>>n>>m;
	for(int i=1;i<=n;i++)
		for(int j=1;j<=m;j++)
			cin>>a[i][j];
	for(int i=1;i<=n;i++)
		for(int j=1;j<=m;j++)
			f[i][j]=(a[i][j]=='*');
}

void solve()
{
	init();
	for(int k=2;k<=(max(n,m)+1)/2;k++)
	for(int i=k;i<=n;i++)
	{
		for(int j=k;j<=m-k+1;j++)
		{
			if(f[i][j]>=k-1&&f[i][j-1]>=k-1&&f[i][j+1]>=k-1&&f[i-1][j]>=k-1)
				f[i][j]++;
		}
	}
	int ans=0;
	for(int i=1;i<=n;i++)
		for(int j=1;j<=m;j++)
			ans+=f[i][j];
	cout<<ans<<'\n';
}
```

## I

### 题目链接
https://codeforces.com/problemset/problem/1451/C

### 题目大意
给定字符串s1，s2，问能否通过如下转化规则使s1变为s2：
+ 可以任意交换s1中字符的顺序
+ 相邻k个字符相同的话，则可以ascii值都+1

### 思路
只要有k个相同的字母，就可以将它们变为任意大于等于它们的字符。那么就可以让k个相等的字母为一组，其他剩余的字母不能改变。然后从字母a开始比较两个字符串中字母个数的差异。当且仅当前每一个字母都差k倍，且底层的字母能补充上层缺少的字母时，成立。

### 代码
```cpp
int n,k;
string s1,s2;
map<char,int> mp;

void init()
{
	cin>>n>>k;
	cin>>s1>>s2;
	mp.clear();
}

void solve()
{
	init();
	int num=0;
	for(auto c:s1) mp[c]++;
	for(auto c:s2) mp[c]--;
	for(auto e:mp)
	{
		//cout<<e.first<<':'<<e.second<<'\n';
		int nu=e.second;
		if(nu%k) {cout<<"NO\n";return;}
		num+=nu/k;
		if(num<0){cout<<"NO\n";return;}
	}
	cout<<"YES\n";
}
```

## L

### 题目链接
https://codeforces.com/problemset/problem/1443/C

### 题目大意
给定两个长度为n的数组，表示分别表示外卖送达的时间和自己去取餐的时间，外卖可以同时叫，取餐只能一个一个去取，问所需要的最短时间。

### 思路
按照外卖时间排序，每一个外卖时间都可以作为一个分界线，小于当前外卖时间的都点外卖，剩余的自己取。自己取的时间可以用前缀和维护。

### 代码
```cpp
struct S
{
	int a,b;
	bool operator<(S r)
	{
		return a<r.a;
	}
};
int n;
S a[N];
int s[N];

void init()
{
	cin>>n;
	for(int i=1;i<=n;i++)
		cin>>a[i].a;
	for(int i=1;i<=n;i++)
		cin>>a[i].b;
}

void solve()
{
	init();
	sort(a+1,a+1+n);
	for(int i=1;i<=n;i++) s[i]=s[i-1]+a[i].b;
	int ans=2e9;
	for(int i=0;i<=n;i++)
	{
		ans=min(ans,max(s[n]-s[i],a[i].a));
	}
	cout<<ans<<'\n';
}
```