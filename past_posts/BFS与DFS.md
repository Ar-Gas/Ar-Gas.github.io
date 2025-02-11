---
title: BFS与DFS
date: 2023-04-02 09:42:00
tags: [algrithm/算法]
categories: [学习笔记,算法]
mathjax: true
---
两种图，从外文摘抄的，后续有时间了反应一下

<!-- more -->



# Representations of graphs

Graph consists of two components:**node(vertice)** & **edge**

Differing from the definition of edfe,*Graph* can be divided into *directed graph*,*Undirected graph*.And different connection also heve other different property.

Here are two standard ways to represent a graph $G=(V,E)$：

## Adjacency-list 

![adjacency list](https://user-images.githubusercontent.com/111374823/230520438-5515ddf5-fb70-4781-82d6-82178b5298d7.png)

Using list to express edge between the vertex element and followed elements,the [C++ codes](https://www.geeksforgeeks.org/graph-and-its-representations/?ref=lbp) as follows:

```c++
// A simple representation of graph using STL
#include <bits/stdc++.h>
using namespace std;
 
// A utility function to add an edge in an
// undirected graph.
void addEdge(vector<int> adj[], int u, int v)
{
    adj[u].push_back(v);
    adj[v].push_back(u);
}
 
// A utility function to print the adjacency list
// representation of graph
void printGraph(vector<int> adj[], int V)
{
    for (int v = 0; v < V; ++v) {
        cout << "\n Adjacency list of vertex " << v
             << "\n head ";
        for (auto x : adj[v])
            cout << "-> " << x;
               cout<<endl;
    }
}
 
// Driver code
int main()
{
    int V = 5;
    vector<int> adj[V];
    addEdge(adj, 0, 1);
    addEdge(adj, 0, 4);
    addEdge(adj, 1, 2);
    addEdge(adj, 1, 3);
    addEdge(adj, 1, 4);
    addEdge(adj, 2, 3);
    addEdge(adj, 3, 4);
    printGraph(adj, V);
    return 0;
}
```

This way is used to represent a **directed graphs**,and is also a compact way to represent **spare** graphs.

## Adjacency-matrix 

![adjacency matrix](https://user-images.githubusercontent.com/111374823/230520450-4cb0d854-6bff-41cd-8f5b-f1a0ee2d42be.png)

We using adj[i][j] to express the edge form i to j;

Directed graph need to assign adj[i][j]=1 and adj[j][i]=1

Undirected graph only need to assign  adj[i][j]=;

here are the C++ codes:

```c++
#include <iostream>
using namespace std;
 
int main()
{
    // n is the number of vertices
    // m is the number of edges
    int n, m;
    cin >> n >> m;
    int adjMat[n + 1][n + 1];
    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        adjMat[u][v] = 1;
        adjMat[v][u] = 1;
        // for a directed graph with an edge pointing from u
        // to v,we just assign adjMat[u][v] as 1
    }
 
    return 0;
}
```

>when we need to be able to tell quickly if there is an edge connecting two given vertices.


# References

[1].[Basics of Graph Theory](https://www.geeksforgeeks.org/graph-and-its-representations/?ref=lbp)