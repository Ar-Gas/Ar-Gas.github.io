---
title: AcWing 93 递归实现组合型枚举 
date: 2023-09-04 15:34:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

- [ACWing 93](https://www.acwing.com/problem/content/description/95/)

<!--more-->

# AC代码

```c++
#include<bits/stdc++.h>
using namespace std;

int n, m;// n个数

vector<int> chosen;

void calc(int x){
	//剪枝
	if ( (chosen.size() > m) || (chosen.size() + (n - x + 1) < m) )
		return;
	//边界输出
	if (x == n + 1) {
		for(auto i = 0; i < chosen.size(); ++i) {
			cout << chosen[i] << " ";
		}
		cout << '\n';
		return;
	}	
	/*字典序小的在前*/
	// 选择x-分支
	chosen.push_back(x); // 记录x已被选择
	calc(x + 1); // 递归求解子a问题
	chosen.pop_back(); // 回溯
	// 不选x-分支
	calc(x + 1);
}

int main(){
	cin >> n >> m;
	calc(1); 
}
```

# Debug

字典序
