---
title: "[Shortest Path] 최단거리 알고리즘 - Dijkstra, Bellman - Ford, Floyd - Warshall"
last_modified_at: 2025-09-05
categories:
  - Algorithm Contents
comments: true
use_math: true
---

Graph에서 최단거리를 구하는 알고리즘인 Dijkstra, Bellman - Ford, Floyd - Warshall에 대해 알아보자.  
  
## Dijkstra Algorithm  
  
Graph의 가중치가 모두 음이 아닌 그래프에서, 특정한 시작 정점 S와 그래프의 다른 모든 점들 사이의 최단 거리를 구하는 알고리즘이다.  
구현 방법은 2가지가 있다. 첫 번째는 Adjacency Matrix(인접 행렬)를 이용하여 시간복잡도 \O(V^{2})\로 구현하는 것이며, 두 번째는 minHeap(최소 힙)을 이용하여 시간복잡도 \O((V + E)logV)\로 구현하는 것이다.  
  
## Bellman - Ford Algorithm  
  
Graph의 가중치가 음수인 경우에도 사용할 수 있는 최단거리 알고리즘이며, 이 경우 그래프 내부에 음의 사이클이 존재해서는 안 된다.  
  
## Floyd - Warshall Algorithm  
  
Graph의 가중치가 음수인 경우에도 사용할 수 있으며, 임의의 두 점 사이의 최단 거리를 한번에 구하는데 사용하는 알고리즘이다.