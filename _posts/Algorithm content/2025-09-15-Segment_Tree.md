---
title: "[Segment Tree] 세그먼트 트리"
last_modified_at: 2025-09-15
categories:
  - Algorithm Contents
comments: true
use_math: true
---  
  
세그먼트 트리(Segment Tree)는 구간 합 query를 빠르게 수행할 수 있도록 자료를 저장하는 자료구조이다. naive하게 query를 수행하려면 매번 더하기 연산을 일일이 수행해야 하기 때문에, 구간 합 query를 log scale만에 처리해야 하는 경우 Segment Tree를 사용한다.  
  
## Segment Tree의 생성 규칙  

세그먼트 트리의 기본 아이디어는, tree의 node에는 특정 구간합을 미리 저장해놓는다는 것이다.  
  
그 규칙은 다음과 같다.  
- tree의 root node의 경우 index = 1부터 시작하며, original array의 [0 ~ n - 1]의 합을 값으로 가진다.  
- tree의 node의 left child node의 경우 index = 2 * parent index이며, parent node가 커버하는 original array의 범위를 [l ~ r]라고 두면 left chlid node가 커버하는 범위는 [1 ~ mid]이다. 이때 mid = (l + r) / 2이다.  
- tree의 node의 right child node의 경우 index = 2 * parent index + 1이며, parent node가 커버하는 original array의 범위를 [l ~ r]라고 두면 right child node가 커버하는 범위는 [mid + 1 ~ r]이다.  
- 위 규칙을 root node에서부터 재귀적으로 적용하여 tree를 구성한다.  
  
예를 들어, original array = [0 ~ 10]일 때, segment tree의 root node가 커버하는 범위는 [0 ~ 9]가 되고, tree의 2번 node = 2 * 1 = 1번 node(= root node)의 left child node가 커버하는 범위는 [0 ~ 4(= 9 / 2)]가 된다.  
  
이제 세그먼트 트리의 생성 원리는 잘 알았다. 그렇다면 세그먼트 트리를 저장하기 위해서는 어느 정도의 공간이 필요할까?  
  
## Segment Tree가 차지하는 공간의 크기  
  
세그먼트 트리는 항상 Full Binary Tree의 형태를 가진다. 왜냐하면 tree의 어떤 node가 커버하는 범위가 [l ~ r]이라고 할 때, 오직 mid = r인 경우만 right child가 존재하지 않을 수 있다(left child는 l부터 커버를 시작하기 때문에 child가 존재한다면 left child는 반드시 존재해야만 한다). 그러나 이는 l + r = 2r, l = r이므로 leaf node가 되어 애초에 child가 존재하지 않는 node가 된다.  
  
즉, 세그먼트 트리의 node의 child 수는 2 또는 0이다. 1은 불가능하다.  
  
또한, Full Binary Tree에서 'leaf node의 수 = internal node의 수 + 1'임을 수학적 귀납법으로 증명할 수 있다.  
  
original array의 원소가 n개라면, 세그먼트 트리의 leaf node가 n개 존재해야 한다. root node의 height = 0이라고 두면, node 수가 n개 존재하는 level의 height = ceiling(n)이다.  
  
따라서, 세그먼트 트리가 최대 가질 수 있는 node의 수는 h = ceiling(n)이라고 두면, height = 0 ~ height = h까지 꽉 차있는 Perfect Binary Tree가 가지는 node의 수와 같으므로 이는 $$2^{0} + ... + 2^{h} = 2^{h + 1} - 1$$과 같다.  
  
또한 세그먼트 트리는 1 - based이므로, 세그먼트 트리를 배열로 구현할 때는 크기를 $$2^{h + 1}$$로 잡으면 된다. 아래는 이를 나타내는 코드이다.  
  
```java
  SegTree(int size){
    int h = (int)Math.ceil(Math.log(size) / Math.log(2));
    this.tree = new int[1 << (h + 1)];
  }
```  
  
## Segment Tree의 query 수행 과정  
  
세그먼트 트리가 original Array의 구간 [a, b]의 모든 원소의 합을 구하는 과정을 살펴보자.  
  
세그먼트 트리의 root node는 모든 원소의 합을 가지고 있으므로, root node에서 child node로 적절히 내려가며 child node가 커버하는 구간을 이용하여 값을 적절히 취해오면 된다.  
  
임의의 tree의 node가 커버하는 구간을 [l, r]이라고 하자. 이때, 다음과 같은 3가지 경우의 수가 존재한다.  
  
- [a, b]와 [l, r]이 아예 겹치지 않는다.  
- [a, b]안에 [l, r]이 전부 포함된다.  
- [a, b]와 [l, r]이 일부 겹친다.  
  
1번째 경우, 우리가 관심있는 구간[a, b]에서 완전히 벗어났으므로 이러한 요청은 0을 return하면 된다.  
  
2번째 경우, 현재 node가 커버하는 범위가 이미 [a, b]에 전부 포함되므로, 이 경우 child node로 더 깊게 들어갈 필요가 없다. 왜냐하면 현재 범위가 이미 [a, b]에 포함되는데, child node로 내려가면 그 child node가 커버하는 범위도 [a, b]에 포함될 것이기 때문이다.  
따라서 이 경우는 그냥 해당 node가 가지고 있는 값을 return하면 된다.  
  
3번째 경우는, 포함되는 구간도 있고 아닌 구간도 있으므로 child node로 내려가 범위를 더욱 좁혀야 한다. 따라서 이 경우, 양쪽 child node로 내려가 값을 구해야 한다.  
  
아래는 이 과정을 구현한 코드이다.  
```java
  // treeNode : segment tree node number
  // treeStart, treeEnd : segment tree의 'treeNode'가 커버하는 범위
  // orgStart, orgEnd : original Array의 구간 범위. 즉 A[orgStart] + ... + A[orgEnd]  
  // 를 구해야 한다.
  long sum(int treeNode, int treeStart, int treeEnd, int orgStart, int orgEnd){
    if(treeEnd < orgStart || treeStart > orgEnd) return 0;
    if(orgStart <= treeStart && treeEnd <= orgEnd) return tree[treeNode];
    int mid = treeStart + ((treeEnd - treeStart) >> 1);
    return sum(2 * treeNode, treeStart, mid, orgStart, orgEnd) +
            sum(2 * treeNode + 1, mid + 1, treeEnd, orgStart, orgEnd);
  }
```  
  
root node에서 시작하여, 한 level씩 내려가며 재귀가 진행되므로 sum의 시간복잡도는 $$O(logn)$$이다.  
    
## Segment Tree의 Update  
  
세그먼트 트리는 Update 또한 같은 방식으로 효율적으로 수행할 수 있다.  
  
original array의 특정 index에 diff 만큼의 값을 더하고 싶을때, root node에서 시작하여 구간을 절반씩 재귀적으로 쪼개면서 해당 index를 커버하는 tree의 node들의 값에 diff 만큼 더해주면 된다.  
  
아래는 이 과정을 구현한 코드이다.  
```java
  void update(int treeNode, int orgIndex, int treeStart, int treeEnd, int diff){
    if(treeStart > orgIndex || treeEnd < orgIndex) return;
    tree[treeNode] += diff;
    if(treeStart == treeEnd) return;
    int mid = (treeStart + treeEnd) / 2;
    update(2 * treeNode, orgIndex, treeStart, mid, diff);
    update(2 * treeNode + 1, orgIndex, mid + 1, treeEnd, diff);
  }
```  
  
마찬가지로, 구간을 벗어나면 더이상 update를 진행시킬 필요가 없고, leaf에 도달하면 더이상 update를 진행시킬 필요가 없다.  
  
sum과 마찬가지로, root node에서 시작하여 한 level씩 내려가므로 update의 시간복잡도는 $$O(logn)$$이다.  
  
## Segment Tree의 Build  
  
세그먼트 트리를 build하는 초기 과정은 단순히 update를 segment tree의 node 수만큼 수행하면 되지만, 이는 $$O(n * logn) = O(nlogn)$$으로 느리다.  
  
재귀를 이용하여 다음과 같이 더 빠르게 진행할 수 있다.  
```java
  void seg(int currIndex, int treeStart, int treeEnd){
    if(treeStart == treeEnd) tree[currIndex] = orgArray[treeStart];
    else {
      int mid = (treeStart + treeEnd) / 2;
      seg(2 * currIndex, treeStart, mid);
      seg(2 * currIndex + 1, mid + 1, treeEnd);
      tree[currIndex] = tree[2 * currIndex] + tree[2 * currIndex + 1];
    }
  }
```  
  
root node에서 시작하여, leaf에 도달하면 값을 할당 후 종료, 그렇지 않으면 left child와 right child로 쪼개어 작업을 수행한다.  
  
세그먼트 트리의 leaf node 수는 n개, internal node의 수는 n - 1개 이므로, 총 node 수는 O(n)이다. 해당 함수는 모든 node를 1번씩 거치며 각 node에서 상수 시간의 작업을 수행하므로, 시간복잡도는 $$O(n)$$이다.  
  
## Segment Tree 전체 구현 코드  
  
아래는 세그먼트 트리의 전체 구현 코드이다.  
```java
  class SegTree{
    long[] tree;

    SegTree(int size, long[] orgArray){
      int h = (int)Math.ceil(Math.log(size) / Math.log(2));
      tree = new long[(1 << (h + 1))];
      seg(1, 0, orgArray.length - 1);
    }

    void seg(int currIndex, int treeStart, int treeEnd){
      if(treeStart == treeEnd) tree[currIndex] = orgArray[treeStart];
      else {
        int mid = (treeStart + treeEnd) / 2;
        seg(2 * currIndex, treeStart, mid);
        seg(2 * currIndex + 1, mid + 1, treeEnd);
        tree[currIndex] = tree[2 * currIndex] + tree[2 * currIndex + 1];
      }
    }

    void update(int treeStart, int treeEnd, int currIndex, int orgIndex, long diff){
      if(treeStart > orgIndex || orgIndex > treeEnd) return;
      tree[currIndex] += diff;
      if(treeStart == treeEnd) return;
      int mid = (treeStart + treeEnd) / 2;
      update(treeStart, mid, currIndex * 2, orgIndex, diff);
      update(mid + 1, treeEnd, currIndex * 2 + 1, orgIndex, diff);
    }

    long sum(int orgStart, int orgEnd, int currIndex, int treeStart, int treeEnd){
      if(orgStart > treeEnd || orgEnd < treeStart) return 0;
      if(orgStart <= treeStart && treeEnd <= orgEnd) return tree[currIndex];
      int mid = (treeStart + treeEnd) / 2;
      return sum(orgStart, orgEnd, currIndex * 2, treeStart, mid) +
              sum(orgStart, orgEnd, 2 * currIndex + 1, mid + 1, treeEnd);
    }
  }
```  
  