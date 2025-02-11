---
title: Codeforces Round 901 Div. 2 题解
date: 2023-10-04 12:02:00
tags: [题解,Codeforces]
categories: [题解,Codeforces]
mathjax: true
---

# A

## 题解

因为最晚也会在 t =  1 的时候使用工具，如果数不大于 a - 1 ，则全部保留，其他保留a - 1

<!--more-->

## AC Code

```c++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

const int N = 110;

ll n, a, b;

void solve(){
	cin >> a >> b >> n;
	ll sum = b;
	for (ll i = 0; i < n ; i ++){
		ll x;
		cin >> x;
		if (x <= a - 1)
			sum += x;
		else 
			sum += a - 1;
	}
	cout << sum << endl;}

int main(){
	ll t;
	cin >> t;
	while(t --){
		solve();
	}
    return 0;
}
```

# B

## 题解

每次用自己最大的交换对方最小的（如果自己最小的比对方最大的大则不会交换）

- 奇数 k:  先手 J 换一次

- 偶数 k : J换一次，G 交换一次

## AC Code

改了一下 T 佬的代码（k优化）

```c++
/**
 *    author:  tourist
 *    created: 30.09.2023 10:34:57       
**/
#include <bits/stdc++.h>

using namespace std;

#ifdef LOCAL
#include "algo/debug.h"
#else
#define debug(...) 42
#endif

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  int tt;
  cin >> tt;
  while (tt--) {
    int n, m, k;
    cin >> n >> m >> k;
    vector<int> a(n);
    for (int i = 0; i < n; i++) {
      cin >> a[i];
    }
    vector<int> b(m);
    for (int i = 0; i < m; i++) {
      cin >> b[i];
    }
    if (k > 2) {
      k = k % 2 == 0? 2: 1;
    }
    for (int it = 0; it < k; it++) {
      sort(a.begin(), a.end());
      sort(b.begin(), b.end());
      if (it % 2 == 0) {
        if (a[0] < b.back()) {
          swap(a[0], b.back());
        }
      } else {
        if (b[0] < a.back()) {
          swap(b[0], a.back());
        }
      }
    }
    cout << accumulate(a.begin(), a.end(), 0LL) << '\n';
  }
  return 0;
}
```

# C

## 题解

模拟，先每人一苹果，剩余苹果不断切除，看m是否是2的倍数

## AC Code

```c++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

ll solve() {
     ll n, m; cin >> n >> m;
     n %= m;
     ll res = 0;
     int idx = 0;
     while (n) {
         res += n;
         n <<= 1;
         n %= m;
         if (++ idx == 30) return -1;
     }
     return res;
 }

int main(){
	ll T;
	cin >> T;
	while(T --){
		solve();
	}
    return 0;
}
```

# D

## 题解

DP求解，代码来自[Jorban](https://zhuanlan.zhihu.com/p/659222071)

## AC Code

````c++
 int solve() {
     int n; cin >> n;
     map<int, int> mp;
     for (int i = 1; i <= n; i ++) {
         int x; cin >> x;
         mp[x] ++;
     }
     int mex = 0;
     while (mp[mex]) mex ++;
     vector<int> f(mex + 1, 1e9);
     f[mex] = 0;
     for (int i = mex; i; i --)
         for (int j = 0; j < i; j ++)
             f[j] = min(f[j], f[i] + i * (mp[j] - 1) + j);
     return f[0];
 }
````

