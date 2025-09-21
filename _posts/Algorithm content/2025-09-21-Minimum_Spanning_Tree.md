---
title: "[Minimum Spanning Tree] 최소 신장 트리"
last_modified_at: 2025-09-21
categories:
  - Algorithm Contents
comments: true
use_math: true
---  
  
## Minimum Spanning Tree  
최소 신장 트리(Minimum Spanning Tree)란, 주어진 그래프의 모든 정점들을 연결하는 tree이며 해당 tree edge들의 가중치의 합이 최소인 tree를 말한다.  
  
최소 신장 트리는 여러개 존재할 수 있지만, 그 가중치의 합은 유일하다.  
  
Prim's algorithm, Kruskal's algorithm 2가지 알고리즘으로 구할 수 있다.  
  
## Kruskal's Algorithm  
크루스칼 알고리즘은 상당히 직관적이다. 아래와 같은 단계로 진행된다.  

- 그래프의 모든 edge들을 priority queue에 삽입하여, 가중치를 기준으로 오름차순 정렬되도록 한다.  
- 각 edge들을 순서대로 꺼내어, 해당 edge가 연결하는 두 정점이 같은 root를 가지는지, 즉 이 edge를 tree에 포함시켰을 때 cycle이 생긴다면 해당 edge는 버린다. 그렇지 않으면 해당 edge를 최소 신장 트리에 포함시킨다.  
- 모든 edge들에 대해 위 과정을 반복한다.  
  
항상 낮은 가중치부터 고려하므로, 크루스칼 알고리즘에 의해 생성된 tree는 가중치의 합이 최소가 된다. 이는 실제 최소 신장 트리와 크루스칼 알고리즘의 결과 tree가 다르다는 가정을 한 귀류법으로 간단하게 보일 수 있다.  
  
아래는 해당 과정을 코드로 나타낸 것이다. 같은 root 판단은 union / find로 진행한다.  
```java
  PriorityQueue<Edge> kruskal = new PriorityQueue<>(); // Edge class 내부 compareTo 구현

  int weight = 0;
  for(int i = 0; i < E; i++){
    Edge e = kruskal.poll();

    if(find(e.u) != find(e.v)){
      weight += e.w;
      union(e.u, e.v);
    }
  } bw.write(weight+"");

  // union - find
  static int find(int start){
    if(parent[start] == start) return start;
    else return parent[start] = find(parent[start]); // path compression
  }

  static void union(int one, int two){
    int first = find(one);
    int second = find(two);
    if(first != second) parent[second] = first;
  }
```  
  
모든 각 edge에 대해 poll, union / find 연산을 진행한다. Priority Queue는 내부적으로 minHeap로 구성되므로, poll 연산 1번은 O(logE), union / find 연산은 path compression을 사용하므로 거의 O(1)수준이다. 따라서 시간복잡도는 $$O(ElogE)$$이다.  
  
그래프에서는 E의 최댓값이 V의 제곱까지 커질 수 있으므로, 최악의 경우는 $$O(V^{2}logV)$$까지 갈 수도 있다. 따라서, 크루스칼 알고리즘은 그래프가 sparse할 때 유리하다.  
  
## Prim's Algorithm  
프림 알고리즘은 다익스트라와 상당히 비슷하다. 다익스트라의 priority queue version으로 구현하면 얘도 $$O(ElogV)$$로 처리할 수 있지만 그래프가 sparse 할 때는 크루스칼이 (본인 기준)더 편하고, 그래프가 dense할 때는 크루스칼이랑 시간복잡도가 별 차이가 없다.  
  
그러므로 프림 알고리즘은 그래프가 dense할 때를 기준, adjacency matrix로 구현해보도록 하겠다.  
  
앞선 다익스트라 알고리즘에서는 relax operation을 distance[v] > distance [u] + e(u, v)인 경우에 진행하였다. 그러나, 프림 알고리즘은 **distance[v] > e(u, v)**인 경우에 진행한다.  
  
왜냐하면 다익스트라 알고리즘처럼, 어떤 특정 vertex로 가는 누적 경로(=경로의 가중치의 합)이 필요가 없다. distance[v], 즉 최소 신장 트리에 v를 포함하기 위해 사용해야 하는 edge들의 가중치의 합보다 그냥 e(u, v) 간선을 포함해서 u를 포함하는 비용이 더 싼 경우에 갱신하면 된다.  
  
구현은 다익스트라와 완전히 같다. relax operation만 빼고  
아래는 pseudo code로 구현한 프림 알고리즘이다(완전한 구현은 최단거리 알고리즘 게시물에).
```java
  initialize distance[] array with +infinite
  distance[start] = 0

  initialize E[] array with NIL

  for i = 0 to |V| - 1 // MST edge 수는 최대 |V| - 1개
    u = minVertex of Graph // v는 아직 방문하지 않은 정점 중, distance가 가장 작은 정점
    if(distance[u] == +infinite) break // graph의 component가 2개 이상인 경우
    visited[u] = true

    if(u != start) add edge (E[u], u) to MST
    for v = first neighbor of u to last neighbor of u
      if(distance[v] > e(u, v))
        distance[v] = e(u, v)
        E[v] = u
```  
E[] 배열은 MST에 edge를 추가하기 위해 사용하는 배열로, relax가 진행되었을 때만, 즉 **정점 v와 연결되어야 하는 edge는 (E[v], v)이다**를 표현하기 위함이다. relax가 진행되었다는 것은, 더 낮은 가중치로 v라는 정점을 포함할 수 있게 되었다는 것이기 때문이다.  
  
추가로, 프림 알고리즘에서는 어떤 정점이 한번 MST에 들어가면 해당 정점의 distance 값은 갱신되지 않음이 수학적으로 보장되어 있다(수학적 귀납법 + 귀류법을 이용하여 증명 가능). 따라서 relax 시 visited 체크는 필요없다.  
  
다익스트라 알고리즘의 adjacency matrix version과 시간복잡도는 완전히 같다. 따라서 시간복잡도는 $$O(V_{2})$$이다.  
  
- related BaekJoon problem : 1197