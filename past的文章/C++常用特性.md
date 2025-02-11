---
title: C++ 常用特性
date: 2023-04-14 09:45:00
tags: [C++]
categories: [学习笔记,C++]
mathjax: true
---

目前还在学习阶段，此贴无参考价值（2023 06 03）

<!--more-->

# for循环C++11

```c++
// the C++03 way
for (std::vector<int>::const_iterator ci = v.begin(); ci != v.end(); ++ci);

// the C++11 way
for (auto ci = v.cbegin(); ci != v.cend(); ++ci);
```


```c++
for (auto& elem : container) //将迭代器元素的地址
```

```c++
for (const auto& elem : container) 
```

```c++
for (auto x : v) //copy元素的值
```

# 搜索

`binary_search(val)`二分搜索;

`lower_bound(val)`返回第一个`>=` `val`的迭代器;

`upper_bound(val)`返回第一个`>` `val`的迭代器

# zero-initializer

C++支持数组0-初始化

```c++
bool boolArray[ARRAY_SIZE] = { 0 };//等效于memset(boolArray,0,sizeof(Array));
char myarray[ARRAY_SIZE] = {0}//全部是字符0
char* myPtrArray[ARRAY_SIZE] = { 0 };//空指针
```

但是不支持其他数值初始化