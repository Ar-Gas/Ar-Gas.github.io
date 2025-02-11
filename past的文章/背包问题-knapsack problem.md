---
title: 背包问题 knapsack problem
date: 2023-03-08 11:45:00
tags: [题解]
categories: [题解]
mathjax: true
---
0-1背包问题

最长公共字符串（LCS）

完全背包问题（UKP）

<!-- more -->

# 背包九讲

[《背包九讲》原文地址](https://github.com/tianyicui/pack)

# 0-1背包问题 / 0-1 knapsack problem

## 伪代码

边界：$P[0,i] = 0, P[i,0] = 0$

递推式：$P[i,c] = max(P[i-1,c-v[i]]+P[i],P[i-1,c])$

```伪代码
输入:商品数量n,各商品的价值p,各商品的体积v,背包容量C输出:商品价格的最大值,最优解方案

/*初始化*/
创建二维数组P[0.n,0..C]和Rec[0..n,0..C]
for i <- 0 to C do
	P[0,i] <- 0
for i <- 0 to n do
	P[i,0] <- o
	
/*求解表格*/
for i <- 1 to n do
    for c <- 1 to C do
        if (v[i] ≤ c) and (p[i]+P[i- 1,c -v[i]] > P[i- 1, c]) then
            P[i,c]   <- p[i]＋P[i- 1,c一v[i]]
            Rec[i,c] <- 1
        else
            P[i,c]   <- P[i -1,c]
            Rec[i,c] <- 0

/*输出最优解方案*/
for i <- n to 1 do
    if Rec[i, K] = 1 then
    	print 选择商品i
    	K <- K - v[i]
    else
  		print 不选商品i
return P[n,C]
```





## C++代码

Time：$\omicron(nW)$

Space:$\omicron(nW)$

```c++
#include<bits/stdc++.h>
using namespace std;

#define N 100
#define max(a,b) (a)>(b)?(a):(b)

int n,w[N],v[N],W,dp[N][N];//item_limt,each_weight,each_value,weight_limt,max_value_graph

int main() {
    int n=3,w[N]={1,3,3},v[N]={3,1,1},W=5,dp[N][N]={0};//input

    for( int i=0 ; i<=n-1 ; i++ )
       for( int j=1 ; j<=W ; j++ )
          if(w[i]>j)dp[i][j]=dp[i-1][j];
          else dp[i][j
                    ]=max(dp[i-1][j-w[i]]+v[i],dp[i-1][j]);

    cout<<dp[n-1][W]<<endl;//output
}
```

动图：

![0-1 knapsack problem](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dc/Knapsack_problem_dynamic_programming.gif/330px-Knapsack_problem_dynamic_programming.gif)


# 完全背包问题/unbounded knapsack problem (UKP)

最初的思路是每格多加k个，求最值

优化后只需要和少加一个的格子比较

Time：$\omicron(nW)$

Space:$\omicron(nW)$

```c++
#include<bits/stdc++.h>
using namespace std;

#define N 100
#define max(a,b) (a)>(b)?(a):(b)

int dp[N][N]={0},W,n;//dp value max_weight limit_number

int main() {
   n=3;W=5;
   int w[N]={1,3,2},v[N]={1,2,3};//input:each_weight each_value

   for(int i=0;i<n;i++)
       for(int j=0;j<=W;j++)
            if(w[i]>j){
               dp[i+1][j]=dp[i][j];
            }else{
               dp[i+1][j]=max(dp[i][j],dp[i+1][j-w[i]]+v[i]);//rule
            }

   cout<<dp[n][W];//output
}
```









# 参考链接

[【DP】0-1 背包问题](https://juejin.cn/post/6966628401914314789#heading-7)

[oi-wiki-背包DP](https://oi-wiki.org//dp/knapsack/)

[MIT video about DP and knapsack](https://www.youtube.com/watch?v=ocZMDMZwhCY)

[《背包九讲》](https://github.com/tianyicui/pack)