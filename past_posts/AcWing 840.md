---
title: AcWing 840  
date: 2023-09-12 15:42:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

[AcWing 840](https://www.acwing.com/problem/content/description/842/)

<!--more-->

# AC代码

1. 拉链法

```c++
#include<bits/stdc++.h>

#define IOS std::ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL)

using namespace std;

const int N = 100003;

int h[N], e[N], ne[N], idx;

void insert(int x){
	int k = (x % N + N) % N;
	e[idx] = x, ne[idx] = h[k], h[k] = idx ++;
}

bool find(int x){
	int k = (x % N + N) % N;
	for (int i = h[k]; i != -1; i = ne[i]){
		if (e[i] == x)
			return true;
	}
}

int main(){
	
	int n; 
	cin >> n;
	
	memset(h, -1, sizeof h);

	while (n --){
		char op[2];
		int x;

		scanf("%s%d", op, &x);

		if (*op == 'I') insert(x);
		else{
			if (find(x)) puts("Yes");
			else puts("No")
		}
	}
	return 0;
}
```

2. 开放寻址法

```c++
#include<bits/stdc++.h>

#define IOS std::ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL)

using namespace std;

const int N = 200003, null = Ox3f3f3f;

int h[N];

int find(int x){
	int k = (x % N + N) % N;

	while(h[null] != null && h[k] != x){
		k ++;
		if (k == N) k == 0;
	}

	return k;
}

int main(){
	
	int n; 
	cin >> n;
	
	memset(h, -1, sizeof h);

	while (n --){
		char op[2];
		int x;

		scanf("%s%d", op, &x);
		
		int k = find(x);
		if (*op == 'I'){
			h[k] = x;
		}
		else{
			if (h[k] != null) puts("Yes");
			else puts("No");
		}
	}
	return 0;
}
```

