---
title: STL 常用API汇总
date: 2023-03-30 22:08:23
tags: [C++]
categories: [学习笔记,C++]
mathjax: true
---
包括：

常用的STL库接口（vector,deque,list,stack,queue,priority_queue,ste,map）

string的用法

<!--more -->

```c++
container<T>::iterator name //迭代器名称
typedef container::iterator itname //简写
```

# 总括

此贴写的比较早，STL的一些思想和细节并没有触及到，留个坑，后面补，单纯记忆这些接口没有意义

# vector

-   `vector<T> name(size)` establish

1. 元素访问

-   `v.front()` 首  
-   `v.at(pos)`=`v[pos]`返回下标值 
-   `v.back()` 末
-   `v.date()`返回指向第一个元素指针

2. 迭代器

-   `v.begin()` 返回首元素迭代器
-   `v.end()` 末

3. 长度、容量

-   `v.empty()` 返回`true`或`false`
-   `v.size()` 元素数量
-   `v.resize(new_size,(new_ele_value))`修改大小
-   `v.max_size()`最大长度

-   `v.reserve(size)`预留空间
-   `v.capacity()`容器当前容量

4. 元素修改

-   `clear()`清除
-   `insert(pos,val)`or`insert(pos,size,val)`
-   `erase()`
-   `push_buck(val)`尾端插入
-   `pop_back()` 尾端删除
-   `swap()` 交换

# deque

-   `deque<T> name(size)` establish

1. 元素访问

-   `at(pos)`=`dq[pos]`返回值 
-   `front()` 首  
-   `back()` 末   

2. 迭代器、长度与`vector`相同

3. 修改

-   `clear()`清除
-   `insert(pos,val)`or`insert(pos,size,val)`
-   `erase()`
-   `push_front(val)` 头部插入
-   `pop_front()` 头部删除
-   `push_buck(val)`尾端插入
-   `pop_back()` 尾端删除
-   `swap()` 交换

# list

`list`与`deque`基本相同

# stack

-   `stack<T> name` establish 
-   `stack<T> copy(initial)`copy

1. `top()`栈顶元素
2. `push(ele)` 插入
3. `pop()`弹出栈顶元素
4. `size()` 大小
5. `empty()`

# queue

-   `queue<T> name` establish 
-   `queue<T> copy(initial)`copy

1. `front()`首元素
2. `push(ele)` 插入
3. `pop()`弹出队首元素
4. `size()` 大小
5. `empty()`

# priority queue

-   `priority_queue<T> name`

常数复杂度：

1. `top()`堆顶元素
2. `size()` 数量
3. `empty()`

对数复杂度：

1. `push(ele)`插入
2. `pop()`删除

# set 

>multiset:多重集合

-   `set<T> name`
-   `multiset<T> name` repeatable

1. 插入删除

- `insert(ele)` 插入(unreaptable)
- `erase(val)`、`erase(iterator_pos)`、`erase(iterator_first,last)`删除
- `clear()`清空

2. 迭代器

-   `begin()`
-   `end()`

c意为只读，const:

-   `cbegin()`
-   `cend()`

3. 查找

-   `find(ele)`找到返回元素的迭代器，否则返回`end()`
-   `count(ele)`ele数量(set为`0`或`1`)
-   `lower_bound(ele)`返回首个**不小于**`ele`的迭代器，没有返回`end()`
-   `upper_bound(ele)`返回首个**大于**`ele`的迭代器，没有返回`end()`
-   `empty()`
-   `size()`

# map

>multimap:多重映射

- `map<Key, T> name` establish
- `mp[Key]=T`查询删改

1. 插入删除

-   `mp.insert(pair<Key, T>(Key,T))`插入
-   `erase(val)`、`erase(iterator_pos)`、  `erase(iterator_first,last)`删除
-   `clear()`

2. 查询

-   `find(ele)`找到返回元素的迭代器，否则返回`end()`
-   `count(ele)`ele数量(set为`0`或`1`)
-   `lower_bound(ele)`返回首个**不小于**`ele`的迭代器，没有返回`end()`
-   `upper_bound(ele)`返回首个**大于**`ele`的迭代器，没有返回`end()`
-   `empty()`
-   `size()`

# string

`string`的操作有很多，以下是部分整理，完整请看：[参考链接](https://cplusplus.com/reference/string/string/)

`string`用字典序比较

- `std::string name` establish
- `c_str()/date()`转化为`char指针`
- `s.copy(char*,len,pos)`返回`size_t len`

1. 输入/输出

-   `operator<<`
-   `operator>>`
-   `getline(std::cin,input_str)` `cin`可为其他`istream`

2. 长度

-   `size()/length()`（常数）
-   `strlen(s.c_str())`（线性）
-   `max_size()`


3. 查找
-   `find(str,pos)`查找`str`在`pos`（默认`0`）之后的第一次出现，否则返回`string::npos`(`-1`)
-   `substr(pos, len)`从`pos`开始截取字串（最长为`len`）

4. 增删

-   `insert(pos,times,ch)`在`pos`处连续插入`times`次`ch`字符
-   `insert(pos,str)`在`pos`处插入`str`
-   `erase(pos,count)`删除`pos`后`count`个字符，`count`默认`pos`后所有
-   `clear()`
-   `operator[]`
-   `at(pos)`
-   `back()`
-   `front()`
-   `push_back(c)`
-   `pop_back()`弹出末字符

1. 替换

-   `replace(pos,count,str)`从`pos`位置开始`count`个字符的子串替换为`str`
-   `replace(first,last,str)`将`[first,last)`的子串替换为`str`（`first`和`last`均为迭代器）

6. 迭代器

- `begin()` & `end()`

# C++11 哈希-无序关联式容器

C++11有四种：`unordered_map`、`unordered_map`、`unordered_multimap`、`unordered_multiset`



# 参考链接

[1].[https://cplusplus.com/](https://cplusplus.com/)