---
title: A*算法
date: 2024-02-12 12:54:00
tags: [algrithm/算法]
categories: [学习笔记,算法]
---

原文：[Introduction to the A* Algorithm](https://www.redblobgames.com/pathfinding/a-star/introduction.html)，本文略有改动

![image](https://private-user-images.githubusercontent.com/111374823/303946478-1f1fbec0-7cde-47f9-aa47-6d188817ff37.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDc3MjA0NTYsIm5iZiI6MTcwNzcyMDE1NiwicGF0aCI6Ii8xMTEzNzQ4MjMvMzAzOTQ2NDc4LTFmMWZiZWMwLTdjZGUtNDdmOS1hYTQ3LTZkMTg4ODE3ZmYzNy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMjEyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDIxMlQwNjQyMzZaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04YjIxYTdmZWI0NzBkZjhjMGM0N2FhYjNlYzdlYzUyOTMzOWQ5ZWVjOTFmNDgwZWNkMWJmOWRlZTBkN2I2Nzc0JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.2kU0XBuc5U-7XTV-Y6_qdGVqG32fkKdYEd7bLNGqEZM)

<!--more-->

# 介绍

A* 算法是对 Dijkstra 算法的修改，针对单个目的地进行了优化。 Dijkstra算法可以找到到达所有地点的路径； A* 查找到一个位置或多个位置中最近的位置的路径。它优先考虑那些似乎更接近目标的路径。

# 实现

A*基于Djikstra算法和BFS优化而来：

## 广度优先搜索

实现：

```python
frontier = Queue()
frontier.put(start) # ※
reached = set()
reached.add(start)

while not frontier.empty():
   current = frontier.get()
   for next in graph.neighbors(current):
      if next not in reached:
         frontier.put(next)
         reached.add(next)
```

这里循环实际上并不构建路径；而是构建路径。它只告诉我们如何访问地图上的所有内容。这是因为广度优先搜索的用途不仅仅是查找路径；它还可以用于其他用途。

所以让我们修改循环以跟踪我们到达的每个位置的来源，并将 `reached` 集重命名为 `came_from` 表（表的键为已到达的集合）：

```python
frontier = Queue()
frontier.put(start) # ※
came_from = dict() # path A->B is stored as came_from[B] == A
came_from[start] = None

while not frontier.empty():
   current = frontier.get()
   for next in graph.neighbors(current):
      if next not in came_from:
         frontier.put(next)
         came_from[next] = current
```

现在每个位置的 `came_from` 都指向我们来自的地方。这些就像“面包屑”。它们足以重建整个路径。移动十字，看看如何按照箭头方向返回到起始位置的反向路径。

重建路径的代码很简单：沿着箭头从目标向后到起点。路径是一系列边，但通常更容易存储节点：

```python
current = goal # ×
path = []
while current != start: # ※
   path.append(current)
   current = came_from[current]
path.append(start) # optional
path.reverse() # optional
```

## 提前退出

一旦我们找到了目标，我们就可以停止扩大边界，只需简单修改：

```python
   if current == goal: # ×
      break           
```

完整代码：

```python
frontier = Queue()
frontier.put(start) # ※
came_from = dict()
came_from[start] = None

while not frontier.empty():
   current = frontier.get()

   if current == goal:  # ×
      break           

   for next in graph.neighbors(current):
      if next not in came_from:
         frontier.put(next)
         came_from[next] = current
```

## 移动花费

在某些寻路场景中，不同类型的移动有不同的成本。例如，在《文明》中，穿过平原或沙漠可能需要 1 个移动点，但穿过森林或丘陵可能需要 5 个移动点。为此，我们需要 Dijkstra 算法（或统一成本搜索）跟踪移动成本，因此让我们添加一个新变量 `cost_so_far` 来跟踪从起始位置开始的总移动成本。、

我们最终可能会多次访问一个位置，且成本不同，因此我们需要稍微改变一下逻辑。如果某个位置从未到达过，我们不会将其添加到边界，而是如果到达该位置的新路径比之前的最佳路径更好，我们将添加它：

```python
frontier = PriorityQueue() # 改动
frontier.put(start, 0) # ※
came_from = dict()
cost_so_far = dict()
came_from[start] = None
cost_so_far[start] = 0

while not frontier.empty():
   current = frontier.get()

   if current == goal: # ×
      break
   
   # 选择最小的成本
   for next in graph.neighbors(current):
      new_cost = cost_so_far[current] + graph.cost(current, next) # 改动
      if next not in cost_so_far or new_cost < cost_so_far[next]: # 改动
         cost_so_far[next] = new_cost # 改动
         priority = new_cost          # 改动
         frontier.put(next, priority) # 改动
         came_from[next] = current
```

使用优先级队列代替常规队列改变了边界扩展的方式。

1 以外的移动成本使我们能够探索更有趣的图形，而不仅仅是网格。在页面顶部的地图中，移动成本取决于房间之间的距离。移动成本还可用于根据与敌人或盟友的接近程度来避开或优先选择区域。

## 启发式搜索

通过广度优先搜索和 Dijkstra 算法，边界向各个方向扩展。如果您试图找到通往所有位置或许多位置的路径，这是一个合理的选择。然而，一种常见的情况是找到一条仅到达一个位置的路径。让我们向目标扩展边界，而不是向其他方向扩展。首先，我们将定义一个启发式函数，它告诉我们距离目标有多近。：

```python
def heuristic(a, b):
   # Manhattan distance on a square grid
   return abs(a.x - b.x) + abs(a.y - b.y) 
```

在 Dijkstra 算法中，我们使用距起点的实际距离来进行优先级队列排序。相反，在贪婪最佳优先搜索中，我们将使用到目标的估计距离来进行优先级队列排序。将首先探索最接近目标的位置。该代码使用 Dijkstra 算法中的优先级队列，但没有 `cost_so_far` ：

```python
frontier = PriorityQueue()
frontier.put(start, 0)# ※
came_from = dict()
came_from[start] = None

while not frontier.empty():
   current = frontier.get()

   if current == goal: # ×
      break
   
   for next in graph.neighbors(current):
      if next not in came_from:
         priority = heuristic(goal, next) # 改动
         frontier.put(next, priority)
         came_from[next] = current
```

很显然，这些路径并不是最短的。当障碍物不多时，该算法运行得更快，但路径不太好。我们可以解决这个问题吗？是的！

## A*算法

Dijkstra 算法可以很好地找到最短路径，但它会浪费时间探索没有希望的方向。贪婪最佳优先搜索朝有希望的方向探索，但它可能找不到最短路径。 A* 算法同时使用距起点的实际距离和距目标的估计距离。

```python
frontier = PriorityQueue()
frontier.put(start, 0) # ※
came_from = dict()
cost_so_far = dict()
came_from[start] = None
cost_so_far[start] = 0

while not frontier.empty():
   current = frontier.get()

   if current == goal: # ×
      break
   
   for next in graph.neighbors(current):
      new_cost = cost_so_far[current] + graph.cost(current, next)
      if next not in cost_so_far or new_cost < cost_so_far[next]:
         cost_so_far[next] = new_cost
         priority = new_cost + heuristic(goal, next) # 改动
         frontier.put(next, priority)
         came_from[next] = current
```

A* 是两全其美的。只要启发式算法不会高估距离，A* 就会找到一条最佳路径，就像 Dijkstra 算法一样。 A* 使用启发式方法对节点进行重新排序，以便更有可能更快地遇到目标节点。

![img](https://upload.wikimedia.org/wikipedia/commons/5/5d/Astar_progress_animation.gif)

# 参考链接

本文翻译自：[A*算法简介](https://www.redblobgames.com/pathfinding/a-star/introduction.html)