---
title: AcWing 835  
date: 2023-09-12 15:42:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

[AcWing 835](https://www.acwing.com/problem/content/description/837/)

<!--more-->

# AC代码

```c++
/*Trie tree,AcWing 835*/

#include <bits/stdc++.h>

using namespace std;

const int N = 100010;

//idx 当前用的下标
int son[N][26], cnt[N], idx;//下标是0的点，既是根节点，又是空节点 
char str[N];

void insert(char str[]){
	int p = 0;//根节点
	for (int i = 0; str[i] ; i ++){
		int u = str[i] - 'a';//映射字母编号
		if (!son[p][u]) son[p][u] = ++idx;
		p = son[p][u];
	}

	cnt[p] ++;
}

int query(char str[]){
	int p = 0;
	for (int i = 0; str[i]; i ++){
		int u = str[i] - 'a';
		if (!son[p][u]) return 0;
		p = son[p][u];
	}

	return cnt[p];
}

int main(int argc, char const *argv[])
{
	int n;
	scanf("%d", &n);
	while (n --){
		char op[2];
		scanf("%s%s", op, str);
		if (op[0] == 'I')insert(str);
		else printf("%d\n", query(str));
	}
}	
```