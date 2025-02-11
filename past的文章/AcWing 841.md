---
title: AcWing 841  
date: 2023-09-12 15:42:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

[AcWing 841](https://www.acwing.com/problem/content/description/843/)

<!--more-->

-------

# AC代码

```c++
#include<bits/stdc++.h>

#define IOS std::ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL)

using namespace std;

typedef unsigned long long ULL;

const int N = 100010, P = 131;

int n, m;
char str[N];
ULL h[N], p[N];

ULL get (int l, int r){
	return h[r] - h[l -1]*p[r - l + 1];
}

int main(){
	scanf("%d%d%s", &n, &m, str + 1);

	p[0] = 1;
	for (int i = 1; i <= n; ++ i)
	{
		p[i] = p[i - 1] * p;
		h[i] = h[i - 1] * p + str[i];
	}
	while (m --){
		int l1, r1, l2 ,r2;
		scanf("%d%d%d%d", &l1, &r1, &l2, &r2);
	
		if (get(l1, r1) == get(l2,r2)) puts("Yes");
		else puts("No");
	}
	return 0;
}
```
