---
title: HDU 4699 Editor 
date: 2023-09-01 16:40:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目：

- [HDU 4699](https://acm.hdu.edu.cn/showproblem.php?pid=4699)

- [ACWing 128(译)](https://www.acwing.com/problem/content/description/130/)

<!--more-->

# AC代码：

```c++
#include<bits/stdc++.h>
using namespace std;

const int INF = 0x3f3f3f3f;
const int N = 1000005;

int sum[N] = {};
int f[N] = {};

stack<int> A,B;

int main(){

	std::ios::sync_with_stdio(false);
    cin.tie(NULL);

	char c;
	int t;
	f[0] = -INF;
	sum[0] = 0;

	int tmp = 0;
	cin >> tmp;

	while(tmp--){
		cin >> c;
		if(c == 'I'){
			cin >> t;
			A.push(t);
			sum[A.size()] = sum[A.size() - 1] + A.top();
			f[A.size()] = max(f[A.size() - 1], sum[A.size()]);
		}
		if(c == 'D'){
			if(!A.empty())
				A.pop();
		}
		if(c == 'L'){
			if(!A.empty())
			{
				B.push(A.top());
				A.pop();
			}
		}
		if(c == 'R'){
			if(!B.empty())
			{
				A.push(B.top());
				B.pop();
				sum[A.size()] = sum[A.size() - 1] + A.top();
				f[A.size()] = max(f[A.size() - 1], sum[A.size()]);
			}
		}
		if(c == 'Q'){
			cin >> t;
			cout << f[t] << endl;
		}
	}
}
```

# Debug

未考虑栈空情况，导致段错误，添加if判断即可。