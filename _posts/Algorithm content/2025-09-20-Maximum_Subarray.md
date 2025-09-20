---
title: "[Maximum Subarray] 최대 부분배열 문제"
last_modified_at: 2025-09-20
categories:
  - Algorithm Contents
comments: true
use_math: true
---  
  
최대 부분배열 문제(Maximum Subarray Problem)란, 주어진 배열의 subarray의 원소들의 합의 최댓값을 구하는 문제이다.  
  
모든 배열의 수가 양수이거나 음수일때는 의미가 없다. 모든 수가 양수이면 배열 자체가 답이고, 모든 수가 음수이면 0에 가장 가까운 음수 하나만이 답이 된다.  
  
해당 문제의 해법으로는 brute force, divide and conquer, dynamic programming이 있다.  
  
## Brute Force 방법
  
가장 간단하게, array의 원소들 중 2개를 골라서 그 사이에 있는 원소들을 다 더해보면 된다. subarray = [i ... j]라고 하였을 때, 가능한 (i, j) 쌍의 수는 \binom{n}{2}, 각 subarray에 대해서 합을 구해야 하므로 구현시 3중 for문으로 구현될 것이며, 시간복잡도는 $$O(n^{3})$$이 될 것이다.  
  
## Divide And Conquer 방법  
  
배열을 절반으로 나누면, 최대 부분배열 문제의 답이 되는 subarray는 다음 3가지 경우 중 하나이다.  
  
원본 배열을 A[1 ... n]이라고 하고, mid = (1 + n) / 2로 정의하였을 때,  
왼쪽 배열 L = A[1 ... mid], 오른쪽 배열 R = A[mid + 1 ... n]이라고 두자.  
  
그렇다면, 최대 부분배열 문제의 답은  
- L에 완전히 포함되거나  
- R에 완전히 포함되거나  
- L과 R 둘 다에 걸친다  

즉 재귀적으로 풀 수 있다. 대략적인 과정을 Pseudo code로 써보면 아래와 같다.  
  
```java
  maximum_subarray(A, l, r){
    mid = (l + r) >> 1

    if(l == r) return A[low];
    else {
      LMax = maximum_subarray(A, l, mid)
      RMax = maximum_subarray(A, mid + 1, r)
      MidMax = maximum_subarray_cross(A, l, r)

      return Max of (LMax, RMax, MidMax)
    }
  }

  maximum_subarray_cross(A, l, r){
    mid = (l + r) >> 1

    int leftSum = -infinite
    int left = 0
    for i = mid to l
      left += A[i]
      leftSum = Max of (leftSum, left)

    int rightSum = -infinite
    int right = 0
    for i = mid + 1 to r
      right += A[i]
      rightSum = Max of (rightSum, right)

    return Max of (rightSum, leftSum, rightSum + leftSum)
  }
```  
  
코드가 긴데, 원리를 알면 쉽다. **maximum_subarray** 함수는 l과 r 사이의 최대 부분배열을 구하는 함수이고, LMax와 RMax에 divide된 작은 problem에 대한 답을 저장한다.  
  
MidMax 변수는 왼쪽과 오른쪽에 걸쳐있는 배열들 중 최댓값을 가지고 있으며, 최종 답은 이 셋 중 최댓값이다.  
  
**maximum_subarray_cross** 함수가 MidMax를 구하는 함수이다. 왼쪽과 오른쪽의 경계인 mid에서 각자 출발하여, 왼쪽에서는 [x ... mid]가 최대인 x를 구하고, 오른쪽에서는 [mid + 1 ... y]가 최대인 y를 구한다.  
  
두 개의 합만 생각하면 안되고, 저 두 개 또한 후보로 고려해야 한다. 왜냐하면 두 배열의 제약 조건이 mid와 엮여 있기 때문이다(왼쪽은 반드시 end index = mid, 오른쪽은 반드시 start index = mid + 1이기 때문).  
  
시간복잡도에 대한 점화식 T(n) = T(n/2) + \Theta(n) 이 되어, 이를 풀면 시간복잡도 T(n) = $$\Theta(nlogn)$$이다.  
  
## Dynamic Programming 방법  
  
가장 간단한 방법이다. 점화식을 아래와 같이 세운다.  
  
dp[i] = 원본 배열의 [1 ... i]를 고려했을 때, i로 끝나는 subarray 중 원소의 합의 최댓값  
  
으로 정의하면, **dp[i + 1] = dp[i] + array[i + 1] > array[i + 1] ? dp[i] + array[i + 1] : array[i + 1]** 이 된다.  
  
왜냐하면, array[i + 1]의 관점에서 생각해보면, 자신이 maximum subarray에 포함된다면 다음 2가지 경우의 수밖에 없다.  
- 문제의 최종 답이 자기 자신(= i + 1)에서 시작하는 경우
- 문제의 최종 답이 자기 자신에서 시작하지 않는 경우 -> 자신보다 이전 index에서 문제의 최종 답이 시작되어야 한다.  
  
따라서, dp[i] + array[i + 1] > array[i + 1]인 경우는 2번째 경우에 해당하여 자신이 답의 일부로 들어가는 것이고, 그외의 경우는 차라리 자기 자신에서 시작하는 것이 더 값이 높으므로 array[i + 1]의 값이 들어가는 것이다.  
  
이를 코드로 보면 아래와 같다.  
```java
  int[] arr = new int[N];
  
  StringTokenizer st = new StringTokenizer(br.readLine());
  while(N-- > 0) arr[arr.length - 1 - N] = Integer.parseInt(st.nextToken()); // 원소 할당

  int[] dp = new int[arr.length]; dp[0] = arr[0];
  int max = dp[0];
  for(int i = 1; i < arr.length; i++){
    if(dp[i - 1] > 0) dp[i] = arr[i] + dp[i - 1]; // 조건 간단화
    else dp[i] = arr[i];

    max = Math.max(max, dp[i]);
  }
  
  System.out.println(max);
```  
  
한줄로 배열의 원소를 입력받아 문제의 답을 출력한다. 시간복잡도는 for문 하나이므로 $$O(n)$$이다.  
  
- related BaekJoon problem : 10211