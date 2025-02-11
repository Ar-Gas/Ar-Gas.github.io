---
title: kuangbin带你飞 专题五 并查集 
date: 2023-09-12 15:42:00
tags: [题解]
categories: [题解,kuangbin]
mathjax: true
---

# 题单

[[kuangbin带你飞]专题五 并查集](https://vjudge.csgrandeur.cn/contest/276210)

1. POJ 2236 Wireless Network
2. POJ 1611 The Suspects
3. HDU 1213 How Many Tables
4. HDU 3038 How Many Answers Are Wrong
5. POJ 1182 食物链
6. POJ 1417 True Liars
7. POJ 1456 Supermarket
8. POJ 1733 Parity game
9. POJ 1984 Navigation Nightmare
10. POJ 2492 A Bug’s Life
11. POJ 2912 Rochambeau
12. ZOJ 3261 Connections in Galaxy War
13. HDU 1272 小希的迷宫
14. POJ 1308 Is It A Tree?

--------

# 并查集

<!--more-->

并查集主要用于解决一些**元素分组**的问题。它管理一系列**不相交的**集合，并支持两种操作：

- **合并**（Union）：把两个不相交的集合合并为一个集合；
- **查询**（Find）：查询两个元素是否在同一个集合中。

### 初始化

```c++
int fa[MAXN];
inline void init(int n)
{
    for (int i = 1; i <= n; ++i)
        fa[i] = i;
}
```

一开始，先将节点的父节点设为自己。

### 查询

```c++
int find(int x)
{
    if(fa[x] == x)
        return x;
    else
        return find(fa[x]);
}
```

利用**递归**的写法实现对代表元素的查询；

要判断两个元素是否属于同一个集合，只需要看它们的根节点是否相同即可。

### 合并

```c++
inline void merge(int i, int j)
{
    fa[find(i)] = find(j);
}
```

### 合并（路径压缩）

```c++
int find(int x)
{
    if(x == fa[x])
        return x;
    else{
        fa[x] = find(fa[x]);  //父节点设为根节点
        return fa[x];         //返回父节点
    }
}
```

简写：

```c++
int find(int x)
{
    return x == fa[x] ? x : (fa[x] = find(fa[x]));
}
```

# 按秩合并

### 初始化（按秩合并）

```c++
inline void init(int n)
{
    for (int i = 1; i <= n; ++i)
    {
        fa[i] = i;
        rank[i] = 1;
    }
}
```

### 合并（按秩合并）

```c++
inline void merge(int i, int j)
{
    int x = find(i), y = find(j);    //先找到两个根节点
    if (rank[x] <= rank[y])
        fa[x] = y;
    else
        fa[y] = x;
    if (rank[x] == rank[y] && x != y)
        rank[y]++;                   //如果深度相同且根节点不同，则新的根节点的深度+1
}
```

# 加权并查集

## 查找（加权）

```c++
//并查集 （带权）
int find(int x){
    if(x != parent[x]){
        int tmp = parent[x];
        parent[x] = find(parent[x]);
        value[x] += value[tmp];
	}
    return parent[x];
}
```

## 合并（加权）

```c++
//并查集 （带权）
void Union(int x, int y, int s){
    int parent_x = find(x);
    int parent_y = find(y);
    if(parent_x != parent_y){
        parent[parent_x] = parent_y;
        value[parent_x] = value[parent_y] - value[parent_x] + s;
	}
}
```


---------

# 题解

## [A - Wireless Network](https://vjudge.csgrandeur.cn/problem/POJ-2236)

```c++
#include<iostream>
#include<algorithm>
#include<cstring>

using namespace std;

const int    N   = 1e3 + 50;

struct Node
{
	int x, y;
}node[N];


// n, d
int n;
double d;

// 父节点，记录数组
int pre[N], vis[N];


// 初始化
void init (){
	for (int i = 1; i <= n; i ++){
		pre[i] = i;
	}	
}

// 查找
int find(int x){
	if (x == pre[x]){// 找到父节点
		return x;
	}else{
		return pre[x] = find(pre[x]); //递归继续找寻+路径压缩
	}
}

// 合并
void merge(int a, int b){
	// pow()
	double D = (node[a].x - node[b].x) * (node[a].x - node[b].x) + (node[a].y - node[b].y) * (node[a].y - node[b].y); 
	// 判断距离
	int dx = find(a);
	int dy = find(b);
	if (dx != dy && d*d >= D)
		pre[dx] = dy; // 合并
}

int main()
{
	cin >> n >> d;
	init();

	for (int i = 1; i <= n; ++ i)
	{
		cin >> node[i].x >> node[i].y;
	}

	char op[2];
	while (~scanf("%s", op)){
		if (op[0] == 'O'){
			int a;
			cin >> a;
			vis[a] = 1;
			for (int i = 1; i <= n; i ++){
				if (vis[i]){
					merge(a, i);
				}
			}
		}
		else if (op[0] == 'S'){
			int a, b;
			cin >> a >> b;
			if (find(a) == find(b)){
				cout << "SUCCESS" << endl;
			}else{
				cout << "FAIL" << endl;
			}
		}
	}

	return 0;
}
```

# 





# 参考资料

[算法学习笔记(1) : 并查集 - 知乎用户Pecco（本文是此文的简化）](https://zhuanlan.zhihu.com/p/93647900)

[并查集 - oi.wiki](https://oi-wiki.org//ds/dsu/#%E5%85%B6%E4%BB%96%E5%BA%94%E7%94%A8)

[并查集续 - 带权并查集 - 知乎why666](https://zhuanlan.zhihu.com/p/477583203)
