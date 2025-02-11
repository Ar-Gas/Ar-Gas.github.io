---
title: AcWing 842 
date: 2023-10-03 13:25:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

- [ACWing 842](https://www.acwing.com/problem/content/description/844/)


<!--more-->

# AC代码

```c++
#include <bits/stdc++.h>

using namespace std;

const int N = 10;

int n;
int path[N];
bool st[N];

void dfs(int u){
	if (u == n){
		for (int i = 0; i < n; i ++)
			printf("%d ", path[i]);
		puts("");
		return ;
	}

	for (int i = 1; i <= n; i ++){
		if (!st[i]){
			path[u] = i;
			st[i] = true;
			dfs(u + 1);
			st[i] = false; //h
		}
	}
}

int main(){
	cin >> n;
	dfs(0);
    return 0;
}
```
