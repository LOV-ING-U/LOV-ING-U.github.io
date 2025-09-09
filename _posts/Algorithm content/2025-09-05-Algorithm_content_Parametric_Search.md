---
title: "[Parametric Search] Binary Search를 이용한 정답 도출"
last_modified_at: 2025-09-09
categories:
  - Algorithm Contents
comments: true
use_math: true
---

Binary Search의 실행 시간이 log scale임을 이용하여 주어진 조건에서 답을 빠르게 구할 수 있다.  
  
Binary Search의 경우 Data가 정렬되어 있어야 하므로, 주어진 Data의 order가 명백할 경우 사용할 수 있다. 예를 들어 오름차순 정렬된 list에서 특정 값을 찾는 경우 주어진 Data가 정렬되어 있다는 가정 하에 binary search를 사용할 수 있다.  
  
예시 문제로 보면 훨씬 쉽게 감을 잡을 수 있다.  
  
Parametric Search의 대표적 예시는 BOJ 1654번 랜선 자르기 문제이다. 해당 문제에서 답을 naive하게 찾기 위해서는 2의 31제곱 개수 만큼의 정수를 검사해야 하는데, 이는 시간상 불가능하다.  
  
따라서, Binary Search의 아이디어를 이용한다. 특정 길이 l로 자른다는 가정 하에, 해당 길이 미만으로 자르는 경우 잘려진 랜선의 개수가 l개로 잘랐을 때의 개수보다 같거나 많을 것이고, 해당 길이보다 길게 자르는 경우 잘려진 랜선의 개수가 l개로 잘랐을 때의 개수보다 같거나 적을 것이다.  
  
따라서, A[l] = "랜선의 길이를 l로 설정하여 잘랐을 때 얻는 랜선의 개수"라고 정의하면, 해당 배열은 반드시 내림차순 정렬된 상태이므로 binary search를 사용하여 upper bound를 구하면 답을 찾을 수 있다.  
  
```java  
  BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));

  String[] ss = br.readLine().split(" ");
  int K = Integer.parseInt(ss[0]);
  int N = Integer.parseInt(ss[1]);

  long[] len = new long[K];

  long max = -1; long min = 1;
  for(int i = 0; i < K; i++){
    long input = Long.parseLong(br.readLine());
    max = Math.max(max, input);
    len[i] = input;
  }

  while(min <= max){
    long midLength = (max + min) / 2;

    //check
    long count = 0;
    for(int i = 0; i < K; i++) count += len[i] / midLength;

    if(count < N) max = midLength - 1;
    else min = midLength + 1;
  }
  bw.write(max+"");

  bw.close();
  br.close();
```  
  
랜선 길이는 자연수이므로, 반드시 답을 정확하게 찾을 수 있다. 본문에서 설명한 A[l] 배열을 따로 만드는 대신 각 len[i]마다 count를 계산하여 A[l] 배열의 원소를 length마다 계산할 수 있다.  
  
while문은 최대 log(랜선의 최대 길이) 횟수 만큼 시행되고, while문 시행마다 count 계산을 위한 K번의 계산이 실행되므로 시간복잡도는 최대 $$O(KlogL)$$이지만, L의 제한 때문에 $$logL\leq 31$$이므로 위 프로그램의 실행 시간은 실제로는 매우 짧다.  
  