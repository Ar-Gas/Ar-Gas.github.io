---
title: KMP算法
date: 2023-09-08 20:36:00
tags: [题解]
categories: [题解,AcWing]
mathjax: true
---

# 题目

[AcWing 831](https://www.acwing.com/problem/content/description/833/)

<!--more-->

# 代码

```c++
#include<bits/stdc++.h>

using namespace std;

const int N = 100010, M = 1000010;

int n, m;
char p[N], s[M];
int Next[N];

int main() {
    cin >> n >> p + 1 >> m >> s + 1;
    
	//求解Next[i]过程
    for (int i = 2, j = 0; i <= n; i++) {
        while (j && p[i] != p[j + 1]) j = Next[j];
        if (p[i] == p[j + 1]) j++;
        Next[i] = j;
    }
    
	//kmp匹配过程
    for (int i = 1, j = 0; i <= m; i++) {
        while (j && s[i] != p[j + 1]) j = Next[j];
        if (s[i] == p[j + 1]) j++;
        if (j == n) {
            printf("%d ", i - n); /* 本题下标从0开始，与上文不同 */
            j = Next[j];
        }
    }
    return 0;
}
```

# 参考

1. [AcWing 831详解](https://www.acwing.com/blog/content/2347/)
2. [KMP算法图解（无代码）](https://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)
