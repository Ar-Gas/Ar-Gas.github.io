---
title: AcWing 93 递归实现组合型枚举 
date: 2023-09-04 15:34:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

[AcWing 94](https://www.acwing.com/problem/content/description/96/)

<!--more-->

# AC代码

```c++
#include<bits/stdc++.h>
using namespace std;

const int N = 20;

int n;// n个数

int order[N];
bool chosen[N];

void calc(int k){
	//边界输出
	if (k == n + 1) {
		for(int i = 1; i <= n; i++)
			printf("%d ", order[i]);
		puts("");
		return;
	}	
	for(int i = 1; i <= n; i++){
		if(chosen[i]) continue; 
		//k位选择i
        order[k] = i;
		chosen[i] = 1;
		calc(k + 1);
		//k位不选择i
        chosen[i] = 0;//回溯
	}
}

int main(){
	cin >> n;
	calc(1); 
}
```

