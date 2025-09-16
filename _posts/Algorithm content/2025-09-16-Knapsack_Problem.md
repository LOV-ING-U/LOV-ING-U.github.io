---
title: "[Knapsack Problem] 배낭 문제"
last_modified_at: 2025-09-15
categories:
  - Algorithm Contents
comments: true
use_math: true
---  
  
배낭 문제(Knapsack Problem)는 배낭에 담을 수 있는 무게의 한도가 정해져 있는 상황에서 물건을 배낭에 최대한으로 담아 배낭에 담긴 물건의 가치를 최대화 하는 문제이다.  
  
배낭에 담는 물건은 무게와 가치를 속성으로 가지며, 배낭은 최대 수용 가능 무게를 속성으로 가진다.
  
배낭 문제는 다음과 같이 2가지 유형으로 나뉜다.  
  
- 배낭에 담을 물건을 분할할 수 있는 경우(Fractional Knapsack Problem)  
- 배낭에 담을 물건을 분할할 수 없는 경우(0 - 1 Knapsack Problem)  
  
## 0 - 1 Knapsack Problem  
기본적으로 배낭에 어떠한 물건을 가치가 최대가 되도록 하여 담는 것을 고려할 때, 물건의 가성비(= 가치 / 무게)를 기준으로 greedy algorithm을 생각해볼 수 있다.  
  
그러나, 이 경우 물건을 분할할 수 없기 때문에 greedy algorithm의 결과가 최적해가 되지 않는다. 작은 반례로, 아래와 같이 배낭의 한도와 각 물건의 가치 및 무게를 설정해보면,  
  
- 배낭의 최대 수용 가능 무게(한도) : 100 
- 물건들의 (가치, 무게) : (100000, 99), (50000, 2)  
  
가성비를 기준으로 물건들을 기준으로 정렬하면 (50000, 2), (100000, 99)가 된다. 이때 가성비가 높은 물건들을 순서대로 담으면 무게가 2인 하나의 물건만 담을 수 있으므로 greedy algorithm의 답은 50000이다.  
  
그러나, 무게가 99인 물건을 먼저 담으면 배낭에 담긴 물건의 가치가 100000이 되므로 이 경우 실제 답은 100000이다.  
  
그러므로, 이 경우에는 greedy algorithm이 아닌, 각 물건들에 대해 존재하는 '담는다 / 담지 않는다'의 2가지 선택을 모두 고려한 동적 계획법(Dynamic Programming) 또는 모든 경우의 수를 실제로 시뮬레이션해보는 백트래킹(BackTracking)을 사용하여야 한다.  
  
일반적으로 백트래킹은 입력의 크기가 커지면 걸리는 시간이 지수적으로 커지므로, 이 문제는 동적 계획법이 올바른 풀이 방법이다.  
  
각 물건의 선택지는 '담는다 / 담지 않는다'의 2가지 뿐이므로, 다음과 같이 점화식을 세울 수 있다.  
  
dp[i][j] = "i번째 물건까지 고려했고, 현재 배낭의 무게가 j 이하일 때 배낭에 담을 수 있는 물건의 가치의 합의 최댓값"이라고 정의하면,  
  
(1 ~ i - 1)번 물건까지 고려했을 경우에 i번 물건을 넣는 상황에서,  
  
dp[i][j] = dp[i - 1][j] (= i번째 물건을 담지 않았을 경우) 와  
           dp[i - 1][j - i.weight] + i.value (= i번째 물건을 담았을 경우) 중 최댓값  
이 될 것이다.  
  
따라서 dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - i.weight] + i.value)가 된다.  
이를 코드로 구현할 때는, 기본값으로 dp[i - 1][j]를 넣어준 후에 (j >= i.weight)인 경우에만 두번째 항을 고려하면 된다.  
  
아래 코드는 이를 구현한 코드이다.  
```java
  int[][] dp = new int[N + 1][K + 1]; // N : 물건의 개수, K : 배낭의 최대 한도

  for(int i = 1; i <= N; i++){
    for(int j = 1; j <= K; j++){
      dp[i][j] = dp[i - 1][j];
      if(j >= list.get(i - 1).weight) dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - list.get(f - 1).weight] + list.get(f - 1).value);
    }
  }  // answer : dp[N][K]
```  
  
시간복잡도는 for문이 중첩되어 있으므로 $$O(NK)$$가 된다.  
  
## 0 - 1 Knapsack Problem : 1 - dimensional Dynamic Programming  
위 코드를 자세히 보면, dp[i][j]를 계산할 때는 바로 이전 행의 정보만 필요함을 알 수 있다. 그러므로, 2차원 배열로 정보를 저장하는 대신 1차원 배열로 정보를 저장할 수도 있다.  
  
아래는 이를 구현한 코드이다.  
```java
  for(int i = 1; i <= N; i++){
    for(int j = K; j >= list.get(i - 1).weight; j--){
      dp[j] = Math.max(dp[j], dp[j - list.get(i - 1).weight] + list.get(i - 1).value);
    }
  }
```  
  
i = 1부터 시작하여(= 첫번째 물건부터 고려하여), 이전과 같은 원리로 Math.max를 통해 값을 결정한다.  
  
다만, 안쪽 반복문의 j가 감소하면서 dp 배열을 계산하는데, 이는 dp[j]를 계산할 때 j보다 더 작은 index의 dp값(= dp[j - i.weight])을 사용하여 계산하기 때문이다. 만약 반복문의 j가 증가한다면 dp[j - i.weight]의 값이 손상되어 dp[j]를 올바르게 계산하지 못하기 때문이다.  
  
추가로, j = 1부터 시작하면 같은 물건을 반복해서 담을 수 있는 형태가 된다. 예를 들어, dp[1] = 3이 되었을 때 dp[2] 계산에서 dp[1]의 값을 사용한다면, 이미 dp[1] = 3인 값이 dp[2] 계산에서 한번 더 더해질 수 있다.  
  
시간복잡도는 $$O(NK)$$로 동일하며, 공간복잡도가 $$O(NK)$$에서 $$O(K)$$로 개선되었다.  
  
## Fractional Knapsack Problem  
이 경우는 간단하다. 가성비가 좋아도 무게가 커서 담지 못했던 물건은 물건을 쪼개서 담을 수 있으므로, 최대의 무게를 채운다고 할 때 가성비가 가장 좋은 물건들만 담는다면 배낭에 담긴 물건들의 총 가치의 합은 항상 최대가 될 것이다.  
  
그러므로 이 경우에는 greedy algorithm의 해가 최적해이다.  
  
구현도 훨씬 간단하다. 아래는 해당 문제의 pseudo code이다.  
```java
  sort all products with ((double)value / weight) descending order
  
  for all products
    if capacity <= 0
      break
    else if weight < capacity
      total value += value
      capacity -= weight
    else
      total value += value * ((double)capacity / weight)
      capacity = 0

  return total value
```  
  
N개의 물건이 있을때, sort는 $$O(NlogN)$$이 걸리며, for문의 경우 최악의 case가 모든 물건에 대해 1번씩 고려하게 되는 것이므로 $$O(N)$$이다. 따라서, 전체 시간복잡도는 정렬 단계가 지배하게 되며 $$O(NlogN)$$이 된다.  
  