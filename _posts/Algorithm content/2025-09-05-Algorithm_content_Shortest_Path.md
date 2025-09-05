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
구현 방법은 2가지가 있다. 첫 번째는 Adjacency Matrix(인접 행렬)를 이용하여 시간복잡도 $$O(V^{2})$$로 구현하는 것이며, 두 번째는 minHeap(최소 힙)을 이용하여 시간복잡도 $$O((V + E)logV)$$로 구현하는 것이다.  
  
1. Adjacency Matrix  
```java
    static void Dijkstra(int start){
      distance[start] = 0;
      int v;
      for(int i = 1; i < graph.length; i++){
        v = minVertex();
        if(v == -1) break;

        visited[v] = true;
        if(distance[v] == INF) return;
            
        for(int w = 0; w < weight[v].size(); w++){
          int index = weight[v].get(w).v;

          if(distance[index] > distance[v] + weight[v].get(w).weight){
            distance[index] = distance[v] + weight[v].get(w).weight;
          }
        }
      }
    }

    static int minVertex(){
      int min = INF;
      int v = -1;

      for(int i = 1; i < distance.length; i++){
        if(!visited[i] && distance[i] < min) {
          v = i; min = distance[i];
        }
      }
      
      return v;
    }
```  
  
distance[] 배열은 원점(start)으로부터의 최단 거리를 저장하는 배열이다. minVertex() 함수는 방문하지 않은 정점 중 가장 거리가 짧은 vertex를 반환하며, 조건에 맞는 정점이 없을 경우 -1을 반환한다.  
  
Dijkstra 함수의 for문이 1번 수행될 때마다, 어떠한 정점 하나가 minVertex()의 output으로서 Dijkstra algorithm을 수행하므로 반복문은 (V - 1)번 수행하면 된다.  
  
Dijkstra의 경우, 어떠한 정점 v에 대해 minVertex의 output으로 나오는 순간 distance[v]가 최단 거리임이 수학적으로 보장된다.

따라서 Dijkstra function의 for문이 (V - 1)번 수행되고, minVertex() 함수 내부에서 각 정점들을 1번씩 검사하므로 전체 시간복잡도는 $$O(V^{2})$$로 결정된다.  
  
2. Priority Queue  
  
  
## Bellman - Ford Algorithm  
  
Graph의 가중치가 음수인 경우에도 사용할 수 있는 최단거리 알고리즘이며, 이 경우 그래프 내부에 음의 사이클이 존재해서는 안 된다.  
  
## Floyd - Warshall Algorithm  
  
Graph의 가중치가 음수인 경우에도 사용할 수 있으며, 임의의 두 점 사이의 최단 거리를 한번에 구하는데 사용하는 알고리즘이다.