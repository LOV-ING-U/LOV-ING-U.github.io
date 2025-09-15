---
title: "[Fenwick Tree] 펜윅 트리(= Binary Indexed Tree, BIT)"
last_modified_at: 2025-09-09
categories:
  - Algorithm Contents
comments: true
use_math: true
---
  
## Fenwick Tree의 생성 규칙  

펜윅 트리(Fenwick Tree)란, 세그먼트 트리에서 한 단계 진화한 자료구조로서 bit 연산을 통해 데이터를 저장하고 관리하는 Tree형 자료구조이다.  
  
Fenwick Tree의 시작 index는 1부터 시작하며, 세그먼트 트리와 마찬가지로 배열의 어떠한 연속된 구간의 합을 node의 value로 가지는데 그 규칙이 특이하다.  
  
Fenwick Tree의 index = i일 때 해당 node에 저장되는 값 = 원본 배열의 index i부터 시작하여, i를 2진법으로 표현한 후 LSB에서 MSB 방향으로 bit를 탐색하였을 때 가장 먼저 1이 나오는 자릿수의 2의 거듭제곱 만큼의 개수만큼의 합(Fenwick Tree와 원본 배열은 1-based라고 가정)  
  
말이 어렵다. 예시로 보면 쉽다. index = 4일 때, 4는 2진법으로 100(2) 이므로, Fenwick Tree의 index = 4에 저장되는 값은, 원본 배열의 index = 4부터 시작하여 2^2(LSB부터 탐색했을 때 가장 먼저 1이 나오는 자리가 2번째 자리이므로) = 의 개수만큼의 합, 즉 A[4] + A[3] + A[2] + A[1]의 값이 저장된다.  
  
index = 6이면 6 = 110(2)이므로, 고려하는 개수는 2^1 = 2개이며 Fenwick Tree의 index = 6에 저장되는 값 = A[6] + A[5]이다.  
  
같은 방식으로, index가 홀수이면 반드시 A[index] 자기 자신만 저장된다. 홀수는 2진법으로 나타내면 0번째 자리가 무조건 1이기 때문이다.  
  
## Fenwick Tree의 Update  

정보를 위와 같은 방식으로 저장하기는 하지만, Update 요청이 들어오면 어떻게 될까? 예를 들어 원본 배열 A[]의 9번째 index의 값을 수정한다면 어떻게 될까? 그렇다면, 9번째 index와 관련된 Fenwick tree의 index를 전부 찾아 해당 index의 값을 tree 상에서 수정해주어야 할 것이다.  
  
우선 index = 9의 값을 수정했을 때, Fenwick Tree 상에서 영향을 받는 index는 다음과 같다 : 9, 10, 12, 16.  
  
9 = 1001(2), 10 = 1010(2), 12 = 1100(2), 16 = 10000(2)이다. 여기서 관찰할 수 있는 것은, 가장 먼저 1이 나오는 bit의 자리가 한 자리씩 이동한다는 것이다. 즉, 9에서 10으로 넘어갈 때는 1을 더하고, 10에서 12로 넘어갈 때는 가장 먼저 1이 나오는 bit인 10(2) = 2를 더하고, 12에서 16으로 넘어갈 때는 100(2) = 4를 더하는 방식이다.  
  
잘 생각해보면 Fenwick Tree를 생성할 때, 가장 먼저 1이 나오는 자리를 파악하여 tree를 구성했었다. 따라서, 방금 예시 = 9를 통해 설명한 update 로직은 tree를 구성할 때 해당 index를 포함하는 모든 경우의 수에 대해 고려하여 update를 진행한 것이며, 그 모든 경우의 수는 Fenwick Tree의 구성 방식에 따라 "가장 먼저 나오는 1의 위치"만 찾으면 모두 고려할 수 있는 것이다.  
  
그렇다면, 가장 먼저 나오는 1의 위치는 어떻게 찾을까?  
  
이 부분은 조금 tricky하다. 컴퓨터는 음수를 2의 보수로 표현하는데, 모든 비트를 뒤집은 후 맨 끝자리에 1을 더한 결과가 해당 수의 음수를 표현한다. 어떤 수 N의 음수를 구하기 위해 일단 모든 비트를 다 뒤집었다면(이를 Nnew라 하자), N과 Nnew를 and 연산 시 당연히 0이 나올 것이다.  
  
예를 들면, N = 1000....0(2)일 때 Nnew = 0111....1(2)가 될 것이다. 이때 Nnew에 1을 더하면, 0번째 자리부터 1에 의해 받아올림이 일어나며 1이었던 것이 0이 되며, 최종적으로 Nnew + 1 = 1000....0(2)가 될 것이다. 즉, 기존 수 N에서 0이었던 부분은 모든 비트를 뒤집음으로 인해 1이 되고, 거기에 1을 더하면 연쇄적으로 받아올림이 일어나며 다시 0으로 바뀌게 될 것이다. 언제까지? 기존 수 N에서 1이었던 부분이 최초로 등장할 때까지. 왜냐하면 N에서 1이었으면 부호 반전 후 0이 되고, 자신보다 낮은 자릿수에서 받아올림이 일어나 carry가 전달되면 다시 1이 된 후 받아올림이 해당 자릿수에서 끊기기 때문이다.  
  
즉 기존 수를 부호 반전 후, 1을 더한 것과 기존 수를 and 연산하면 우리가 원하는 수를 얻을 수 있다. 이는 2의 보수 표현법과 완전 일치하므로, 어떤 index = i의 값을 수정해야 된다는 요청이 들어오면 i를 처리한 후에 다음 index = i + (i & -i)로 단순화할 수 있다.  
  
원리는 되게 복잡한데, 구현은 아래와 같이 매우 간단하다.  
```java
  void Update(int idx, int diff){
    while(idx < tree.length){
      tree[idx] += diff;
      idx += (idx & (-idx));
    }
  }  
```  
  
## Fenwick Tree의 query 수행 과정  

펜윅 트리는 세그먼트 트리와 마찬가지로 1번의 구간합 요청을 log 시간 단위로 처리할 수 있다. A[1] + ... + A[idx] 즉 index = 1부터 index = idx까지 모든 원소의 합을 요청하면 어떻게 될까?  
  
(편의상 tree = Fenwick Tree) tree[idx]가 가지고 있는 값은, 펜윅 트리의 생성 원리에 따라, A[idx]부터 시작하여 최초로 0이 아닌 bit가 나오는 자릿수의 2의 거듭제곱 만큼의 개수를 포함한다. 따라서, 이 경우도 동일하게 최초로 0이 아닌 bit가 나오는 자릿수를 찾아 이를 계속해서 원래 index에 빼면 된다. 이를 index >= 1이 될 때까지 반복하면, 결국 A[idx] + ... + A[1]의 값을 구할 수 있다.  
```java
  long sum(int idx){
    long val = 0;
    while(idx > 0){
      val += tree[idx];
      idx -= (idx & (-idx));
    }
    return val;
  } 
```  
  
## Fenwick Tree 전체 구현 코드 및 시간복잡도 분석  

코드 전문은 아래와 같다.
```java
  class FenWickTree{
    long[] tree;

    FenWickTree(int[] orgArray){
      tree = new long[orgArray.length + 1]; // 1 - based
      for(int j = 1; j < tree.length; j++) Update(j, orgArray[j - 1]);
    }

    void Update(int idx, int diff){
      while(idx < tree.length){
        tree[idx] += diff;
        idx += (idx & (-idx));
      }
    }

    long sum(int idx){
      long val = 0;
      while(idx > 0){
        val += tree[idx];
        idx -= (idx & (-idx));
      }
      return val;
    }
  }
```  
  
변수 tree가 fenwick tree가 된다. 초기 initialization은 Update 함수를 n번 부르면 된다.  
  
시간복잡도를 분석해보자.  
  
Update 함수의 경우, while문이 돌아가는 횟수가 시간복잡도를 결정한다. 이때 fenwick tree의 생성 원리에 따라, 가장 먼저 1이 나오는 자릿수부터 그 자릿수가 하나씩 증가하며, 이는 tree.length = n에 도달하면 멈춘다. 자릿수가 하나씩 증가한다는 것은 2를 계속 곱하는 것과 같으며, 따라서 Update query의 시간복잡도는 $$O(logn)$$이다.  
  
Sum 또한 동일하다. 반대로 n부터 시작하여 index >= 1을 만족할 때까지 자릿수를 하나씩 내려가는 것과 같으므로, 시간복잡도는 $$O(logn)$$으로 동일하다.  
  
## Fenwick Tree의 Fast Build  

추가로, Fenwick Tree안의 initialization을 단순히 Update를 n번 부르게 하는 것이 아닌 빠르게 build할 수도 있다.  
```java
  for(int i = 1; i < tree.length; i++) tree[i] = orgArray[i] // org array copy
  for(int i = 1; i < tree.length; i++) {
    int j = i + (i & -i);
    if (j < tree.length) tree[j] += tree[i];
  }
``` 
(Fenwick Tree의 Fast Build)  
  
작은 index가 커버하는 구간을, 큰 index = j에 더해주는 것이다.  
tree[4]로 예시를 들어보자.  
  
i = 1일때 tree[1]은 org array copy에 의해 A[1]만을 들고 있었다. i = 1일 때 j = 2가 되고, tree[2] += tree[1]이 수행된다. 즉 tree[1]이 가지고 있었던 구간 [1, 1]을, tree[2]가 흡수함으로써 tree[2]가 커버하는 구간은 [2, 2]에서 [1, 2]가 된 것이다.  
  
마찬가지로 i = 2일 때, j = 4가 되어 tree[4] += tree[2]가 수행되고, 결과적으로 tree[2]가 가지고 있던 구간 [1, 2]를 tree[4]가 흡수하여 tree[4]는 index [1, 2, 4]를 커버하게 된 것이다.  
  
i = 3일 때 또 다시 j = 4가 되어 tree[4] += tree[3]이 수행되어, 비로소 tree[4]가 커버해야 하는 구간인 [1, 4]를 완전히 커버하게 되는 것이다.  
  
i = 4일 때는, j >= 5가 되어 tree[4]에 대한 작업이 완전히 종료되고, 결과적으로 tree[4]는 [1, 4]를 정상적으로 커버하게 된다.  
  
핵심 원리는, i = 1부터 시작하여 어떠한 특정 index가 커버하는 구간을 잘게 쪼개어 작은 index에서부터 합쳐준다는 것이다.  
  
기존 방식(Update를 n번 부르는)은 logn의 시간을 잡아먹는 Update를 n번 불렀으므로 $$O(nlogn)$$이고, Fast Build Version은 for문만 수행하면 되어 $$O(n)$$이 되므로 훨씬 빨라진다.  
  