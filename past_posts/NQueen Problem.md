---
title: NQueen problem
date: 2023-04-03 08:51:00
tags: [题解]
categories: [题解]
mathjax: true
---
N皇后（八皇后）问题、棋盘问题

递归DFS解法

<!-- more -->

# NQueen

回溯法主要是两个部分，检查位置和DFS

```c++
int isright(int r,int c){
    for(int x=0;x<r;x++){//check with previous line 
       if(c==col[x])return false;//if col is not repeated
       if(abs(col[x]-c)==abs(x-r))return false;//if diagonal is coincided
    }
    return true;
}
```

```c++
int dfs(int row){
    if(row==n){ans++;print();return 0;}//stop circulation
    for(int i=0;i<n;i++){//try every col
        if(isright(row,i)){
            col[row]=i;//settle
            dfs(row+1);//recurse
            col[row]=0;//back 
        }
    }
}
```

一维数组，下标表示行，值表示列：
```c++
vector<int>col(8);//val is queen_col,rank is queen_row
```


完整代码为：（输入：$n$,输出：遍历方案、方案总数）

```c++
#include<bits/stdc++.h>
using namespace std;

int ans,n;//answer,number_row(n*n)
vector<int>col(8);//val is queen_col,rank is queen_row

int isright(int r,int c){
    for(int x=0;x<r;x++){//check with previous line 
       if(c==col[x])return false;//if col is not repeated
       if(abs(col[x]-c)==abs(x-r))return false;//if diagonal is coincided
    }
    return true;
}

void print(){
    for(int a=0;a<n;a++)cout<<col[a]+1;
    cout<<'\n';
}

int dfs(int row){
    if(row==n){ans++;print();return 0;}//stop circulation
    for(int i=0;i<n;i++){//try every col
        if(isright(row,i)){
            col[row]=i;//settle
            dfs(row+1);
            col[row]=0;//back 
        }
    }
}

int main(){
    ans=0;
    cin>>n;
    dfs(0);//start from first row;
    cout<<ans;  
    return 0;
}
```

# 棋盘问题 POJ-1321

[问题链接](https://vjudge.csgrandeur.cn/problem/POJ-1321)，AC代码：

```c++
#include<iostream>

using namespace std;

#define N 8

int ans,n,k;//answer,size(n*n),piece_number
char bound[N][N];
bool col[N]={0};//each col ishave?

bool check(int c){
    if(col[c])return false;
    return true;
}

void dfs(int row,int count){
    if(count==k ){ans++;return;}
    if(row==n ){return;}
    for(int i=0;i<n;i++){
        if(bound[row][i]=='#'&& check(i)){
            col[i]=1;//settle
            dfs(row+1,count+1);//First condition:settled 
            col[i]=0;//clear
        }
    }
    dfs(row+1,count);//Second condition:unsettled 
}

int main(){
    while(cin>>n>>k){
        ans=0;
        if(n==-1&&k==-1)break;
        for(int i=0;i<n;i++)
            for(int j=0;j<n;j++)
                cin>>bound[i][j];
        dfs(0,0);
        cout<<ans<<'\n';   
    }
}
```