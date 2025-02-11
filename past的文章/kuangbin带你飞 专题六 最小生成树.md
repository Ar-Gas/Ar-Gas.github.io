---
title: kuangbin带你飞 专题六 最小生成树 
date: 2023-09-22 20:42:00
tags: [题解]
categories: [题解,kuangbin]
mathjax: true
---

# 题单

[[kuangbin带你飞]专题六 最小生成树 ](https://vjudge.csgrandeur.cn/contest/276211)

| A    | [POJ 1251 POJ 1251](https://vjudge.csgrandeur.cn/problem/POJ-1251/origin) | [Jungle Roads  丛林道路](https://vjudge.csgrandeur.cn/contest/276211#problem/A) |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| B    | [POJ 1287  POJ 1287](https://vjudge.csgrandeur.cn/problem/POJ-1287/origin) | [Networking  联网](https://vjudge.csgrandeur.cn/contest/276211#problem/B) |
| C    | [POJ 2031  POJ 2031](https://vjudge.csgrandeur.cn/problem/POJ-2031/origin) | [Building a Space Station 建造空间站](https://vjudge.csgrandeur.cn/contest/276211#problem/C) |
| D    | [POJ 2421  POJ 2421](https://vjudge.csgrandeur.cn/problem/POJ-2421/origin) | [Constructing Roads  修建道路](https://vjudge.csgrandeur.cn/contest/276211#problem/D) |
| E    | [ZOJ 1586  佐吉1586](https://vjudge.csgrandeur.cn/problem/ZOJ-1586/origin) | [QS Network  QS 网络](https://vjudge.csgrandeur.cn/contest/276211#problem/E) |
| F    | [POJ 1789  邮政编码 1789](https://vjudge.csgrandeur.cn/problem/POJ-1789/origin) | [Truck History  卡车历史](https://vjudge.csgrandeur.cn/contest/276211#problem/F) |
| G    | [POJ 2349  邮政编码 2349](https://vjudge.csgrandeur.cn/problem/POJ-2349/origin) | [Arctic Network  北极网络](https://vjudge.csgrandeur.cn/contest/276211#problem/G) |
| H    | [POJ 1751  邮政编码 1751](https://vjudge.csgrandeur.cn/problem/POJ-1751/origin) | [Highways  高速公路](https://vjudge.csgrandeur.cn/contest/276211#problem/H) |
| I    | [POJ 1258  POJ 1258](https://vjudge.csgrandeur.cn/problem/POJ-1258/origin) | [Agri-Net  农业网](https://vjudge.csgrandeur.cn/contest/276211#problem/I) |
| J    | [POJ 3026  POJ 3026](https://vjudge.csgrandeur.cn/problem/POJ-3026/origin) | [Borg Maze  博格迷宫](https://vjudge.csgrandeur.cn/contest/276211#problem/J) |
| K    | [POJ 1679  邮政编码 1679](https://vjudge.csgrandeur.cn/problem/POJ-1679/origin) | [The Unique MST 独特的MST](https://vjudge.csgrandeur.cn/contest/276211#problem/K) |
| L    | [HDU 1233  HDU 1233](https://vjudge.csgrandeur.cn/problem/HDU-1233/origin) | [还是畅通工程](https://vjudge.csgrandeur.cn/contest/276211#problem/L) |
| M    | [HDU 1301  HDU 1301](https://vjudge.csgrandeur.cn/problem/HDU-1301/origin) | [Jungle Roads  丛林道路](https://vjudge.csgrandeur.cn/contest/276211#problem/M) |
| N    | [HDU 1875  HDU 1875](https://vjudge.csgrandeur.cn/problem/HDU-1875/origin) | [畅通工程再续](https://vjudge.csgrandeur.cn/contest/276211#problem/N) |

--------

# 最小生成树

## 简介

**普里姆算法**（Prim算法），图论中的一种算法，可在加权连通图里搜索最小生成树。意即由**此算法搜索到的边子集所构成的树中，不但包括了连通图里的所有顶点（英语：Vertex (graph theory)），且其所有边的权值之和亦为最小**。该算法于1930年由捷克数学家沃伊捷赫·亚尔尼克（英语：Vojtěch Jarník）发现；并在1957年由美国计算机科学家罗伯特·普里姆（英语：Robert C. Prim）独立发现；1959年，艾兹格·迪科斯彻再次发现了该算法。因此，在某些场合，普里姆算法又被称为DJP算法、亚尔尼克算法或普里姆－亚尔尼克算法。

## prime

1. 输入：一个加权连通图，其中顶点集合为V，边集合为E；

2. 初始化：Vnew = {x}，其中x为集合V中的任一节点（起始点），Enew = {},为空；

3. 递归下列操作，直到Vnew = V：
   1. 在集合E中选取权值最小的边<u, v>，其中u为集合Vnew中的元素，而v不在Vnew集合当中，并且v∈V（如果存在有多条满足前述条件即具有相同权值的边，则可任意选取其中之一）；
   2. 将v加入集合Vnew中，将<u, v>边加入集合Enew中；

4. 输出：使用集合Vnew和Enew来描述所得到的最小生成树。

## 代码














---------

# 题解





# 参考资料

[最小生成树-Prim算法和Kruskal算法 - 博客园-华山大师兄](https://www.cnblogs.com/biyeymyhjob/archive/2012/07/30/2615542.html)
