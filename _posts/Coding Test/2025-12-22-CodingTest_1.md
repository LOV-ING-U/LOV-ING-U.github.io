---
title: "2024 KAKAO WINTER INTERNSHIP Coding Test 문제풀이"
last_modified_at: 2025-12-22
categories:
  - Coding Test
---

학기 종강하고 진짜 오랜만에 글을 쓰는 것 같다.  
오랜만에 코딩 테스트 문제를 풀려니 뭔가 새롭게 느껴지기도 하고 실제 기출문제들의 풀이를 기록해놓는 것도 좋겠다 싶어서 새로 카테고리를 하나 만들어서 기록할 것이다.  
  
문제는 프로그래머스에 모두 공개되어 있다. <https://school.programmers.co.kr/learn/challenges?order=recent>
  
# 1. 가장 많이 받은 선물  
단순한 구현 문제. 2차원 배열 하나를 선언해서 선물을 주고받은 관계를 표현하고, 각 사람이 총 받은/준 선물 갯수를 기록하는 1차원 배열 하나를 더 선언해서 선물 지수를 계산한다.  
  
진짜 그냥 문제 지문 주어진대로만 구현하면 된다. 코드는 아래와 같다.
```Java
import java.util.*;
import java.io.*;

class Solution {
    public int solution(String[] friends, String[] gifts) {
        int flen = friends.length;
        
        // "i" -> "j" count
        int[][] from_to_store = new int[flen][flen];
        
        // "i" <- (element)
        int[] receive_total = new int[flen];
        
        // "i" -> (element)
        int[] give_total = new int[flen];
        
        // friends[i] = index i
        HashMap<String, Integer> mapping_name_to_idx = new HashMap<>();
        for(int i = 0; i < flen; i++) mapping_name_to_idx.put(friends[i], i);
        
        int glen = gifts.length;
        for(int i = 0; i < glen; i++){
            String[] from_to = gifts[i].split(" ");
            
            int from = mapping_name_to_idx.get(from_to[0]);
            int to = mapping_name_to_idx.get(from_to[1]);
            
            receive_total[to]++;
            from_to_store[from][to]++;
            
            give_total[from]++;
        }
        
        // "i" -> "j"
        int[][] total_count_gift = new int[flen][flen];
        for(int i = 0; i < flen; i++){
            for(int j = i + 1; j < flen; j++){
                // "i" and "j"
                
                // if there is record, and not equal
                if(from_to_store[i][j] + from_to_store[j][i] > 0 && from_to_store[i][j] != from_to_store[j][i]){
                    if(from_to_store[i][j] > from_to_store[j][i]) total_count_gift[j][i]++;
                    else total_count_gift[i][j]++;
                } else {
                    int i_calculate = give_total[i] - receive_total[i];
                    int j_calculate = give_total[j] - receive_total[j];
                    
                    if(i_calculate > j_calculate) total_count_gift[j][i]++;
                    else if(i_calculate < j_calculate) total_count_gift[i][j]++;
                }
            }
        }
        
        int max = Integer.MIN_VALUE;
        for(int j = 0; j < flen; j++){
            int tmp = 0;
            for(int i = 0; i < flen; i++) tmp += total_count_gift[i][j];
            
            max = (max < tmp) ? tmp : max;
        }
        
        return max;
    }
}
```  
  
# 2. 도넛과 막대 그래프  
처음 봤을때 어떻게 풀어야하지? 생각이 들었는데 막대 모양을 제외한 나머지 2개 모양이 사이클이므로 진입 간선이 최소 1개 이상 있어야 한다는 점을 이용해야겠다는 생각이 들었다. 진입 간선이 없으면 막대 모양의 시작점이거나 / 추가된 정점이 되고, 문제 조건에 의해 진출 간선이 2개 이상이면 추가된 정점이다.  
  
추가된 정점을 알았으므로, 해당 정점에서 연결된 각 정점에서 DFS를 1번씩 돌리면 그때마다 component 1개씩을 얻을 수 있고, 8자 모양 그래프는 사이클이 2개 있으므로 DFS를 수행 중에 탐색 중인 정점을 2번 만나고 / cycle 그래프는 사이클이 1개이므로 DFS 수행 중 탐색 중인 정점을 1번 만나는 것으로 구분하였다. 막대모양 그래프는 component를 1번에 완전히 탐색하지는 못하지만 탐색 중인 정점을 만나는 일은 없다.  
  
이를 코드로 구현하면 아래와 같다.  
```Java
import java.util.*;

class Solution {
    boolean[] visited;
    List<Integer>[] graph = new List[1000001];
    int meet_occured;
    
    public int[] solution(int[][] edges) {
        // should be no in-edge + at least two out-edge
        
        int edge_count = edges.length;
        
        for(int i = 1; i <= 1000000; i++) graph[i] = new ArrayList<>();
        int[] in_edge = new int[1000001];
        
        for(int i = 0; i < edge_count; i++){
            int[] e = edges[i];
            
            in_edge[e[1]]++;
            graph[e[0]].add(e[1]);
        }
        
        // 1. vertex number
        int vertex_num = 0;
        for(int i = 1; i <= 1000000; i++){
            if(in_edge[i] == 0 && graph[i].size() >= 2){
                vertex_num = i;
                break;
            }
        }
        
        // 2. component sum = out_edge[vertex_num]
        // while DFS,
        // 1) 8-shape : meet already visited vertex twice
        // 2) cycle : meet already visited vertex once
        int eight_shaped = 0;
        int cycle = 0;
        
        int total_component_count = graph[vertex_num].size();
        visited = new boolean[1000001];
        
        for(int i = 0; i < total_component_count; i++){
            meet_occured = 0;
            
            int e = graph[vertex_num].get(i);
            visited[e] = true;
            DFS(e);
            
            if(meet_occured == 2) eight_shaped++;
            else if(meet_occured == 1) cycle++;
        }
        
        return new int[]{vertex_num, cycle, total_component_count - cycle - eight_shaped, eight_shaped};
    }
    
    // return count of already meeting vertex
     void DFS(int source){
        int adj_count = graph[source].size();
        
        for(int i = 0; i < adj_count; i++){
            int e = graph[source].get(i);
            
            if(!visited[e]){
                visited[e] = true;
                DFS(e);
            } else meet_occured++;
        }
    }
}
```  
  
# 3. 주사위 고르기  
처음 봤을때 이걸 다 해봐야하나 생각이 들었는데, 이걸 다 해보려면 총 경우의 수가 $$6^{10} \times \binom{10}{5}$$가 되어서 무조건 시간초과 날 것 같았다. 어차피 모든 경우의 수를 다 검색하기는 해야 하므로 A와 B를 나눠서 탐색하면 $$2 \times 6^{5} \times \binom{10}{5}$$로 줄어들고, A와 B가 가진 각 주사위에 대해 나올 수 있는 모든 합의 경우의 수에 대해 그 횟수를 저장하는 배열을 가지고 있으면 해당 배열을 순회하며 경우의 수를 누적 저장하면 된다.  
  
코드는 아래와 같다.  
```Java
import java.util.*;

class Solution {
    int n;
    
    int[] a_sum;
    int[] b_sum;
    
    int[] a_dice;
    int[] b_dice;
    
    int[][] dice_;
    
    public int[] solution(int[][] dice) {
        n = dice.length;
        int two_n = 1 << n;
        
        dice_ = dice;
        
        a_dice = new int[n / 2];
        b_dice = new int[n / 2];
        
        int[] return_val = new int[n / 2];
        
        int max = -1;
        for(int nl = 0; nl < two_n; nl++){
            if(Integer.bitCount(nl) != n / 2) continue;
            
            for(int j = 0, a = 0, b = 0, t = nl; j < n; j++){
                if(t % 2 == 0) b_dice[b++] = j;
                else a_dice[a++] = j;
                
                t >>= 1;
            }
            
            a_sum = new int[501];
            b_sum = new int[501];
            
            simulate_a(0, 0);
            simulate_b(0, 0);
            
            // count all choice
            int tmp = 0;
            for(int j = 0; j < 501; j++){
                if(b_sum[j] == 0) continue;
                
                for(int i = j + 1; i < 501; i++){
                    tmp += a_sum[i] * b_sum[j];
                }
            }
            
            if(max < tmp){
                max = tmp;
                
                for(int j = 0; j < n / 2; j++) return_val[j] = a_dice[j] + 1;
            }
        }
        
        Arrays.sort(return_val);
        return return_val;
    }
    
    void simulate_a(int depth, int total_sum){
        if(depth == n / 2){
            a_sum[total_sum]++;
            return;
        }
        
        for(int i = 0; i < 6; i++){
            simulate_a(depth + 1, total_sum + dice_[a_dice[depth]][i]);
        }
    }
    
    void simulate_b(int depth, int total_sum){
        if(depth == n / 2){
            b_sum[total_sum]++;
            return;
        }
        
        for(int i = 0; i < 6; i++){
            simulate_b(depth + 1, total_sum + dice_[b_dice[depth]][i]);
        }
    }
}
```  
  
# 4. n + 1 카드게임  
문제들 중에 이게 제일 어려운 것 같다. n이 1000이라 일일이 다 해보는 것도 불가능하고, 그다음으로 생각했던게 DP였는데 DP로 전달할 정보가 단순화되지 않는 것 같았다. 그러다 문득 '카드 배치는 어차피 고정이니, 같은 라운드까지 왔다면 코인이 더 많은 경우가 최적해가 아닐까?'하는 생각이 들어 그리디 알고리즘으로 접근해야겠다는 생각이 들었다.  
  
과정은 다음과 같다. 기본 카드를 제외하고, 카드를 새로 2장씩 받을 때마다 일단 후보 list(= may_cards)에 저장한 후, 후보 list에 있는 카드들과 기본 카드들 중에 합을 n + 1인 쌍을 제출한다. 제출할 때 코인 소모를 최소화 해야하므로, 우선 기본 카드들 중에 쌍의 존재유무를 확인하고, 없으면 추가된 카드와 기본 카드 1장씩을 내며 코인을 1개 소모, 이러한 경우도 없으면 추가된 카드들에서 모두 2장을 내며 코인을 2개 소모하고 다음 단계로 넘어간다.  
  
코드는 아래와 같다.  
  
```Java
import java.util.*;
import java.util.stream.*;

class Solution {
    int n;
    List<Integer> cards_set = new ArrayList<>();
    List<Integer> may_cards = new ArrayList<>();
    
    public int solution(int coin, int[] cards) {
        n = cards.length;
        int init_cards = n / 3;
        
        for(int i = 0; i < init_cards; i++) cards_set.add(cards[i]);
        
        int round = 1;
        for(int i = init_cards; i < n; i += 2){
            int first_card = cards[i];
            int second_card = cards[i + 1];
            
            may_cards.add(first_card);
            may_cards.add(second_card);
            
            // consume 0 coins
            if(find('c')){
                round++;
                continue;
            }
            
            // consume 1 coins
            if(coin >= 1 && find('m')){
                round++;
                coin--;
                continue;
            }
            
            // consume 2 coins
            if(coin >= 2 && find('2')){
                round++;
                coin -= 2;
                continue;
            }
            
            break;
        }
        
        return round;
    }
    
    boolean find(char c){
        int card = cards_set.size();
        int may = may_cards.size();
        
        if(c == 'c'){
            // cards_set(2)
            for(int i = 0; i < card; i++){
                for(int j = i + 1; j < card; j++){
                    if(cards_set.get(i) + cards_set.get(j) == n + 1){
                        cards_set.remove(j);
                        cards_set.remove(i);
                        
                        return true;
                    }
                }
            }
            
            return false;
        } else if(c == 'm') {
            // cards_set(1) + may_cards(1)
            for(int i = 0; i < card; i++){
                for(int j = 0; j < may; j++){
                    if(cards_set.get(i) + may_cards.get(j) == n + 1){
                        cards_set.remove(i);
                        may_cards.remove(j);
                        
                        return true;
                    }
                }
            }
            
            return false;
        } else {
            // may_cards(2)
            for(int i = 0; i < may; i++){
                for(int j = i + 1; j < may; j++){
                    if(may_cards.get(i) + may_cards.get(j) == n + 1){
                        may_cards.remove(j);
                        may_cards.remove(i);
                        
                        return true;
                    }
                }
            }
            
            return false;
        }
    }
}
```  
  
# 5. 산 모양 타일링  
백준에 비슷한 문제가 너무 많아서 보자마자 DP로 풀어야겠다고 생각했다. 맨 우측 삼각형 타일의 경우, 평행사변형에 의해 채워지는 것이 아니면 반드시 삼각형으로만 채워져야 한다. 이를 2가지 DP 배열로 나누어 정의하면 답이 쉽게 나온다.  
  
코드는 아래와 같다.  
  
```Java
class Solution {
    public int solution(int n, int[] tops) {
        // dp[i] : 윗변 i, 아랫변 i + 1까지 고려했을 때 채우는 방법의 수
        // dp[i] = dp_exceed[i] + dp_unexceed[i]
        // dp_exceed[i] : 끝 모양이 \ \ 평행사변형인 경우
        // dp_unexceed[i] : 끝 모양이 삼각형인 경우
        
        int[] dp_exceed = new int[n + 1];
        int[] dp_unexceed = new int[n + 1];
        dp_unexceed[0] = 1;
        
        for(int i = 1; i <= n; i++){
            if(tops[i - 1] == 1){
                dp_exceed[i] = dp_unexceed[i - 1] + dp_exceed[i - 1];
                dp_unexceed[i] = 2 * (dp_unexceed[i - 1] + dp_exceed[i - 1]) + dp_unexceed[i - 1];
                // 맨 우측 모양인 반드시 삼각형,
                // 중앙 역삼각형이
                // 1) 다이아몬드 소속
                // 2) / / 평행사변형
                // 3) 그냥 삼각형
            } else {
                dp_exceed[i] = dp_unexceed[i - 1] + dp_exceed[i - 1];
                dp_unexceed[i] = (dp_unexceed[i - 1] + dp_exceed[i - 1]) + dp_unexceed[i - 1];
            }
            
            dp_exceed[i] %= 10007;
            dp_unexceed[i] %= 10007;
        }
        
        return (dp_exceed[n] + dp_unexceed[n]) % 10007;
    }
}
```  
  
오랜만에 알고리즘 문제를 풀었어서 느낌이 새로웠다. 시간은 5시간이었다고 하는데 지금은 모두 풀었지만 실제 시험장에서는 한번도 풀어본 적이 없으니까 시험장 가서도 내 실력을 100% 발휘할 수 있도록 좀 더 연습을 해야겠다.  
  
아 그리고 주석은 원래 영어로 쓰는게 편하긴 한데, 도저히 영어로 적기 힘든 경우에만 한글로 적고있다... 5번 문제처럼 아이디어를 기록해둬야 하는 문제처럼