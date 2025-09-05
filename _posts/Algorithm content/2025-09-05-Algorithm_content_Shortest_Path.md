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
  
구현 방법은 2가지가 있다. 첫 번째는 Adjacency Matrix(인접 행렬)를 이용하여 시간복잡도 $$O(V^{2})$$로 구현하는 것이며, 두 번째는 minHeap(최소 힙)을 이용하여 시간복잡도 $$O(ElogV)$$로 구현하는 것이다.  
  
- Adjacency Matrix Version  
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

따라서, Dijkstra에서 v == -1 이면 "조건을 만족하는 정점이 없어서 선택되지 않았다"는 것이므로 break하여 중단한다. distance[v] == INF의 경우 "정점이 선택되었지만 start에서 도달 불가능한 경우"를 대비하여 넣었다. 둘 중 하나만 택해도 무방하지만 Defensive Programming 의도로 코드에 넣었다.  
  
Dijkstra 함수의 for문이 1번 수행될 때마다, 어떠한 정점 하나가 minVertex()의 output으로서 Dijkstra algorithm을 수행하므로 반복문은 (V - 1)번 수행하면 된다.  
  
Dijkstra의 경우, 어떠한 정점 v에 대해 minVertex의 output으로 나오는 순간 distance[v]가 최단 거리임이 수학적으로 보장된다.

따라서 Dijkstra function의 for문이 (V - 1)번 수행되고, minVertex() 함수 내부에서 각 정점들을 1번씩 검사하므로 전체 시간복잡도는 $$O(V^{2})$$로 결정된다.  
  
- Priority Queue Version  
```java
  static void Dijkstra(int start){
    PriorityQueue<Node> pq = new PriorityQueue<>(new Comparator<Node>() {
      @Override
      public int compare(Node e1, Node e2) {
        return e1.dist - e2.dist;
      }
    }); pq.add(new Node(start, 0)); distance[start] = 0;

    while(!pq.isEmpty()){
      Node cur = pq.poll();

      for(int x = 0; x < graph[cur.v].size(); x++){
        Edge e = graph[cur.v].get(x);
  
        if(distance[e.v] > cur.dist + e.d){
          distance[e.v] = cur.dist + e.d;
          pq.add(new Node(e.v, distance[e.v]));
        }
      }
    }
  }

  class Node{
   int v;
    int dist;

    Node(int v, int dist){
      this.v = v;
      this.dist = dist;
    }
  }
```  
  
Node class를 새로 정의하여, v에 현재 정점 그리고 dist에 start 정점에서 v까지의 누적 거리를 담는다.  
  
내부적으로 minHeap로 구현된 priorityQueue를 사용해서 dist가 작은 것부터 처리하도록 한다. 왜냐하면, 작은 거리부터 시작하면 해당 정점의 최단 거리가 이미 한번 처리된 후에는 더이상 갱신될 수 없기 때문이다. 따라서 Dijkstra의 수학적 정당성을 위해 dist가 작은 것부터 검사하도록 한다.  
  
위와 같은 원리로 queue가 empty가 될 때까지 relax 작업을 수행해준다.  
  
while문 안의 if문의 경우 정점 cur.v와 e.v가 연결된 경우에만 검사되므로 2E번(undirected graph일 경우), poll시마다 minHeap 내부적으로 minHeap property 유지를 위한 sift 연산이 일어나고 해당 연산은 원소가 삽입된 횟수 즉 최대 $$O(E)$$번(최악의 경우, if문에서 검사하는 족족 queue에 삽입되므로) 이므로 총 시간복잡도는 $$O(ElogE)$$이다.  
  
일반적으로, graph에서 $$E\leq V^{2}$$ 이므로 $$logE\leq 2logV$$이다. 따라서 $$O(ElogE)$$ == $$O(ElogV)$$이다.  
  
  
## Bellman - Ford Algorithm  
  
Graph의 가중치가 음수인 경우에도 사용할 수 있는 최단거리 알고리즘이며, 이 경우 그래프 내부에 음의 사이클이 존재해서는 안 된다. 왜냐하면 음의 사이클이 존재할 경우, 무한정 반복하면 항상 그 전 결과보다 작은 값을 얻기 때문이다.  
```java
  static boolean bellMan_Ford(int start) {
    distance[start] = 0;

    for (int i = 0; i < N; i++) {
      for (Edge e : edges) {
        if(distance[e.u] == INF) continue;

        if(distance[e.v] > distance[e.u] + e.weight){
          distance[e.v] = distance[e.u] + e.weight;

          if(i == (N - 1)) return false;
        }
      }
    }

   return true;
  }

  class Edge{
    int u;
    int v;
    int weight;

    Edge(int u, int v, int weight) {
      this.u = u;
      this.v = v;
      this.weight = weight;
    }
  }
```  
  
Dijkstra와 다르게, 정점이 선택된다고 해서 반드시 그것이 최단거리임을 보장할 수 없다. 음의 간선에 의해 기존 Dijkstar의 수학적 정당성이 깨질 수 있으므로, 1번의 과정마다 반드시 모든 edge를 탐사해야 한다.  
  
정점이 V개인 경우, 최대 V - 1번의 이동 즉 최대 V - 1개의 간선을 타고 가는 것이 상한이므로 for문을 V - 1번 수행하고, 각 for문 1번 수행마다 모든 간선을 전부 검사한다.  
  
for문의 V번째 수행에서 distance 배열 값에 변화가 일어난다면 그것은 해당 정점을 지나는 음의 사이클이 존재한다는 것이다. 어떠한 정점 쌍의 경우에도 최대 V - 1번의 간선 이동으로 도달할 수 있으므로 V - 1번 반복 완료 직후에는 최단 거리가 확정이 되어야 하기 때문이다.  
  
for문은 V번 수행, 각 for문의 수행마다 모든 간선을 검사하므로 총 시간복잡도는 $$O(VE)$$이다.  
  
## Floyd - Warshall Algorithm  
  
Graph의 가중치가 음수인 경우에도 사용할 수 있으며, 임의의 두 점 사이의 최단 거리를 한번에 구하는데 사용하는 알고리즘이다. 마찬가지로 음의 사이클이 존재하는 경우 의미가 없다.  
```java
  for(int k = 0; k < n; k++){
    for(int i = 0; i < n; i++){
      for(int j = 0; j < n; j++){
        graph[i][j] = Math.min(graph[i][j], graph[i][k] + graph[k][j]);
      }
    }
  }
```  
  
초기 graph는 INF로 초기화되어 있는 상태이다(i == j인 경우 graph[i][j] = 0). 간선의 입력을 받은 상태에서 위 과정을 수행한다.  
  
Floyd - Warshall Algorithm의 경우 점화식을 구현해 놓은 것이다. i에서 j로 가는 최단경로는 반드시 어떠한 정점을 건너야 하는데(i와 j가 connected인 경우 자기자신을 건너고, 그렇지 않은 경우 어떠한 정점을 거쳐서 가야 한다), 이때 지나는 정점의 번호를 k라고 두면 graph[i][j] = graph[i][j] 또는 graph[i][k] + graph[k][j]중 작은 것을 고르게 된다.  
  
이를 모든 k에 대해 수행하면 모든 경우의 수를 살피는 것이므로 위 코드의 수행 결과로는 모든 정점 쌍의 최단 거리가 나오게 된다. 즉 graph[i][j] = "i와 j 사이의 최단거리".  
  
이때 반드시 k를 담당하는 for문을 가장 바깥으로 빼야 하는데, 왜냐하면 Floyd - Warshall의 경우 Dynamic Programming을 기반으로 하고 있기 때문이다.  
  
점화식의 완전한 원본은 Dk[i][j] = 거치는 중간 정점이 {1, 2, ..., k}일 때 i와 j 사이의 최단경로로 정의하고, Dk[i][j] = min(D(k - 1)[i][j], D(k - 1)[i][k] + D(k - 1)[k][j])로 정의하여 거치는 중간 정점의 집합을 순차적으로 늘려나가는 것이 핵심이다.  
  
그런데 여기서 for문의 순서를 바꾸면, 즉 k를 안쪽으로 넣어버리면 해당 점화식의 정확성을 보장하는 원리인 "허용 가능한 중간 정점의 집합을 순차적으로 늘려 나간다"는 제약이 깨진다.  
  
따라서 올바른 결과를 얻기 위해서는 k를 담당하는 for문을 가장 바깥으로 뺀 후, k를 고정하여 허용 가능한 중간 정점의 범위를 고정한 후 i와 j 사이의 거리를 계산해야 한다.  
  
총 시간복잡도는 3중 for문이므로 $$O(V^{3})$$이다. V의 개수가 커질수록 시간복잡도는 기하급수적으로 증가하므로 V의 개수가 작은 경우에 대해 사용하여야 안전하다.  
  
  