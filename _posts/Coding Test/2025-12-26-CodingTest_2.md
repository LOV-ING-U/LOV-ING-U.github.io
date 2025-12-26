---
title: "2023 KAKAO BLIND RECRUITMENT 문제풀이"
last_modified_at: 2025-12-26
categories:
  - Coding Test
---

겨울방학을 알차게 살기 위해 아침에는 코테 연습, 점심에는 네트워크 공부 및 프로젝트 수행을 하고 있다. 코테도 오랜만에 보니까 감이 살짝 떨어졌었는데 반복해서 하다보니 다시 감이 올라오고 있는 것 같다. 뭐든지 꾸준히 하는게 중요한듯 하다.  
  
바로 문제 풀어보겠다.  
  
# 1. 개인정보 수집 유효기간  
단순한 구현 문제이다. 모든 달이 28일까지라고 가정했으므로 유효기간을 일 단위로 환산해서 비교해주면 매우 간단하다.  
  
```Java
import java.util.*;

class Solution {
    public int[] solution(String today, String[] terms, String[] privacies) {
        HashMap<String, Integer> terms_date = new HashMap<>();
        for(int i = 0; i < terms.length; i++) {
            String[] person_and_date = terms[i].split(" ");
            terms_date.put(person_and_date[0], Integer.parseInt(person_and_date[1]));
        }
        
        List<Integer> brake = new ArrayList<>();
        for(int i = 0; i < privacies.length; i++){
            String[] date_and_person = privacies[i].split(" ");
            
            // date
            if(should_brake(today, date_and_person[0], terms_date.get(date_and_person[1]))) brake.add(i + 1);
        }
        
        return brake.stream().mapToInt(Integer::intValue).toArray();
    }
    
    boolean should_brake(String today, String date, int available){
        String[] today_ = today.split("\\.");
        String[] start_ = date.split("\\.");
        
        int deadline_int = Integer.parseInt(start_[0]) * 28 * 12 + Integer.parseInt(start_[1]) * 28 +
                      Integer.parseInt(start_[2]) + available * 28 - 1;
        int today_int = Integer.parseInt(today_[0]) * 28 * 12 + Integer.parseInt(today_[1]) * 28 +
                      Integer.parseInt(today_[2]);
        
        return today_int > deadline_int;
    }
}
```  
  
# 2. 택배 배달과 수거하기  
딱 보자마자 가장 먼 거리부터 수거하면 되겠다고 생각하고 구현을 했는데, 아이디어는 맞았는데 구현이 좀 오래 걸렸다.. 구현 연습을 좀 더 할 필요가 있을 듯 싶다.  
  
```Java
import java.util.*;

class Solution {
    public long solution(int cap, int n, int[] deliveries, int[] pickups) {
        long distance = 0;
        int deliver = n - 1; int getback = n - 1;
        int deliver_endpoint = n - 1; int getback_endpoint = n - 1;
        
        if(n == 1) return 2 * (long)Math.ceil((double)Math.max(deliveries[0], pickups[0]) / cap);
        
        while(true){
            while(deliver >= 0 && deliveries[deliver] == 0) deliver--;
            while(getback >= 0 && pickups[getback] == 0) getback--;
            
            deliver_endpoint = deliver;
            getback_endpoint = getback;
            
            if(deliver < 0 && getback < 0) break;
            
            int tmp = cap;
            
            // "deliver" ~ "end"
            while(deliver >= 0 && tmp > 0) {
                tmp -= deliveries[deliver];
                deliveries[deliver--] = 0;
            }
            if(tmp < 0) deliveries[++deliver] = -tmp;
            
            // "getback" ~ "end"
            tmp = cap;
            while(getback >= 0 && tmp > 0) {
                tmp -= pickups[getback];
                pickups[getback--] = 0;
            }
            if(tmp < 0) pickups[++getback] = -tmp;
            
            distance += 2 * (Math.max(getback_endpoint, deliver_endpoint) + 1);
        }
        
        return distance;
    }
}
```  
  
# 3. 이모티콘 할인행사  
이모티콘의 길이가 7 이하이므로 backtracking으로 모든 경우의 수를 다 해보면 된다.  
  
```Java
import java.util.*;

class Solution {
    int[] emoticons_;
    int[][] users_;
    
    int emoticon_count;
    int users_count;
    
    double[] discount;
    double[] discount_percent;
    
    int emoticon_plus_register = 0;
    int emoticon_sell_money = 0;
    
    public int[] solution(int[][] users, int[] emoticons) {
        users_ = users;
        emoticons_ = emoticons;
        
        emoticon_count = emoticons.length;
        users_count = users.length;
        
        discount_percent = new double[4];
        discount_percent[0] = 0.1;
        discount_percent[1] = 0.2;
        discount_percent[2] = 0.3;
        discount_percent[3] = 0.4;
        
        discount = new double[emoticon_count];
        backtrack_emoticon(0);
        
        return new int[]{emoticon_plus_register, emoticon_sell_money};
    }
    
    void backtrack_emoticon(int depth){
        if(depth == emoticon_count){
            // calculate
            
            int emoticon_register = 0;
            int emoticon_money = 0;
            
            for(int i = 0; i < users_count; i++){
                double sell = 0;
                
                for(int j = 0; j < emoticon_count; j++){
                    sell += (((double)users_[i][0] / 100) <= discount[j]) ? (1 - discount[j]) * emoticons_[j] : 0;
                }
                
                if(sell >= users_[i][1]) emoticon_register++;
                else emoticon_money += (int)sell;
            }
            
            if(emoticon_register > emoticon_plus_register){
                // new max
                emoticon_plus_register = emoticon_register;
                emoticon_sell_money = emoticon_money;
            } else if(emoticon_register == emoticon_plus_register) {
                // should max money
                emoticon_sell_money = emoticon_sell_money < emoticon_money ? emoticon_money : emoticon_sell_money;
            }
            
            return;
        }
        
        for(int i = 0; i < 4; i++){
            discount[depth] = discount_percent[i];
            backtrack_emoticon(depth + 1);
        }
    }
}
```  
  
# 4. 표현 가능한 이진트리   
아이디어가 참신한 문제. 포화 이진트리(= perfect binary tree)의 경우 부모-자식간 인덱스 관계가 정해져 있으므로, 이를 이용하면 된다.  

숫자를 이진수로 바꿔 자릿수가 (2의 거듭제곱 - 1)꼴이 아니라면 그렇게 될때까지 앞에 0을 붙이고, 이 string을 반씩 갈라가면서(= 각각 left/right subtree로 간주하면서) 부모가 0인데 자식이 1이면 모순, 그렇지 않으면 가능한 것으로 출력하면 된다.   
  
```Java
import java.util.*;

class Solution {
    public int[] solution(long[] numbers) {
        int[] solution_arr = new int[numbers.length];
        for(int i = 0; i < numbers.length; i++){
            String numbers_to_2 = Long.toBinaryString(numbers[i]);
            int numbers_to_2_length = numbers_to_2.length();
            
            int k = (int)Math.ceil(Math.log10(numbers_to_2_length + 1) / Math.log10(2));
            k = (1 << k) - 1;
            
            int[] binary_rep_num_i = new int[k];
            for(int t = binary_rep_num_i.length - 1, count = numbers_to_2_length - 1; count >= 0; t--, count--){
                binary_rep_num_i[t] = numbers_to_2.charAt(count) - '0';
            }
            
            if(can_rep(binary_rep_num_i, 0, binary_rep_num_i.length)) solution_arr[i] = 1;
        }
        
        return solution_arr;
    }
    
    boolean can_rep(int[] binary_rep, int start, int end){
        // leaf
        if(end == start + 1) return true;
        
        // internal node
        int mid = (start + end) >> 1;
        if(binary_rep[mid] == 0 && (binary_rep[(start + mid) / 2] == 1 || binary_rep[(mid + 1 + end) / 2] == 1)) return false;
        else return can_rep(binary_rep, start, mid) && can_rep(binary_rep, mid + 1, end);
    }
}
```  
  
# 5. 표 병합   
Union-find를 사용하는 문제. 그러나 2차원 배열이므로 인덱스 관리에 유의해야 한다. Merge시 root만 값을 들고 있도록 신경만 써주면 된다.  
  
또 마찬가지로 아이디어는 맞았는데 구현 디테일에서 시간을 많이 잡아먹었다. 구현쪽은 연습을 좀 더 해야겠다..  
  
```Java
import java.util.*;

class Solution {
    int[][] parent = new int[51][51];
    
    public String[] solution(String[] commands) {
        List<String> ans = new ArrayList<>();
        String[][] content = new String[51][51];
        
        for(int i = 1; i < 51; i++){
            for(int j = 1; j < 51; j++) {
                content[i][j] = "EMPTY";
                parent[i][j] = 50 * i + j; // 51(1,1)~100(1,50), 101(2,1)~150(2,50)
            }
        }
        
        for(int t = 0; t < commands.length; t++){
            // command
            String command = commands[t];
            
            String[] command_split = command.split(" ");
            String prefix = command_split[0];
            
            if(prefix.equals("UPDATE")) {
                // r c value
                if(command_split.length == 4){
                    int r = Integer.parseInt(command_split[1]);
                    int c = Integer.parseInt(command_split[2]);
                    String value = command_split[3];
                    
                    int root = find(r, c);
                    content[(root - 1) / 50][(root - 1) % 50 + 1] = value;
                } else {
                    // value1 value2
                    String value1 = command_split[1];
                    String value2 = command_split[2];
                    
                    for(int i = 1; i < 51; i++){
                        for(int j = 1; j < 51; j++){
                            if(find(i, j) == 50 * i + j && content[i][j].equals(value1)) content[i][j] = value2;
                        }
                    }
                }
            } else if(prefix.equals("MERGE")) {
                int r1 = Integer.parseInt(command_split[1]);
                int c1 = Integer.parseInt(command_split[2]);
                
                int r2 = Integer.parseInt(command_split[3]);
                int c2 = Integer.parseInt(command_split[4]);
                
                int root1 = find(r1, c1);
                int root2 = find(r2, c2);
                
                int root1_r1 = (root1 - 1) / 50;
                int root1_c1 = (root1 - 1) % 50 + 1;
                int root2_r1 = (root2 - 1) / 50;
                int root2_c1 = (root2 - 1) % 50 + 1;
                
                String root1_content = content[root1_r1][root1_c1];
                String root2_content = content[root2_r1][root2_c1];
                
                // merge
                union(r1, c1, r2, c2);
                
                if((root1_content.equals("EMPTY") && root2_content.equals("EMPTY")) ||
                   (!root1_content.equals("EMPTY") && !root2_content.equals("EMPTY"))) continue;
                else if(root1_content.equals("EMPTY")) content[root1_r1][root1_c1] = root2_content;
                else content[root1_r1][root1_c1] = root1_content;
            } else if(prefix.equals("UNMERGE")) {
                int r = Integer.parseInt(command_split[1]);
                int c = Integer.parseInt(command_split[2]);
                
                int root = find(r, c);
                List<int[]> fix = new ArrayList<>();
                String val = content[(root - 1) / 50][(root - 1) % 50 + 1];
                for(int i = 1; i < 51; i++){
                    for(int j = 1; j < 51; j++){
                        if(find(i, j) == root) {
                            fix.add(new int[]{i, j});
                        }
                    }
                }
                
                for(int i = 0; i < fix.size(); i++){
                    int[] e = fix.get(i);
                    
                    parent[e[0]][e[1]] = 50 * e[0] + e[1];
                    content[e[0]][e[1]] = "EMPTY";
                }
                
                // fill
                content[r][c] = val;
            } else {
                // print
                int r = Integer.parseInt(command_split[1]);
                int c = Integer.parseInt(command_split[2]);
                
                int root = find(r, c);
                ans.add(content[(root - 1) / 50][(root - 1) % 50 + 1]);
            }
            
            for(int i = 1; i < 51; i++){
                for(int j = 1; j < 51; j++) parent[i][j] = find(i, j);
            }
        }
        
        return ans.stream().toArray(String[]::new);
    }
    
    void union(int r1, int c1, int r2, int c2){
        int a = find(r1, c1);
        int b = find(r2, c2);
        
        if(a != b) parent[(b - 1) / 50][(b - 1) % 50 + 1] = a;
    }
    
    int find(int r1, int c1){
        if(parent[r1][c1] == 50 * r1 + c1) return 50 * r1 + c1;
        else {
            int p = parent[r1][c1];
            return parent[r1][c1] = find((p - 1) / 50, (p - 1) % 50 + 1);
        }
    }
}
```  
  
# 6. 미로 탈출 명령어  
길이가 제한된 상황에서 사전순으로 가장 빠른 탈출 명령어를 출력하는 문제. 문자열이 사전순으로 가장 빠르려면 뒷부분을 자른 substring에 대해서도 사전순으로 가장 빨라야 하므로, DP를 사용할 수 있다고 생각하여 풀이를 진행했다.  
  
dp 배열을 : k번 이동하여 S에서 해당 지점으로 이동하는 방법 중 사전순으로 가장 빠른 것으로 정의를 하고, 각 반복마다 모든 점들을 순회하며 dp 정보를 갱신해주면 간단하게 답이 나온다.  
  
개인적으로 앞부분 문제보다 오히려 뒷부분 문제들이 더 쉬웠던 것 같다. 구현에서 실수가 나냐 아니냐 차이인거 같은데, 구현은 진짜 많이 해보는 방법밖에는 없는 것 같다...  
   
```Java
class Solution {
    public String solution(int n, int m, int x, int y, int r, int c, int k) {
        String[][] dp = new String[n + 1][m + 1];
        String[][] dp_tmp = new String[n + 1][m + 1];
        
        // k = 1
        int[] dy = {0, -1, 0, 1};
        int[] dx = {1, 0, -1, 0};
        String[] direction = {"r", "u", "l", "d"};
        for(int i = 0; i < 4; i++){
            if(x + dy[i] > n || x + dy[i] < 0 || y + dx[i] > m || y + dx[i] < 0) continue;
            dp[x + dy[i]][y + dx[i]] = direction[i];
        }
        
        // k >= 2
        for(int t = 2; t <= k; t++){
            for(int i = 1; i <= n; i++){
                for(int j = 1; j <= m; j++){
                    // dp[i][j]_k -> dp_tmp[i][j] 
                    if(dp[i][j] == null) continue;
                    
                    for(int d = 0; d < 4; d++){
                        if(i + dy[d] > n || i + dy[d] < 0 || j + dx[d] > m || j + dx[d] < 0) continue;
                        
                        // perform dp
                        if(dp_tmp[i + dy[d]][j + dx[d]] == null || dp_tmp[i + dy[d]][j + dx[d]].length() < t) {
                            dp_tmp[i + dy[d]][j + dx[d]] = dp[i][j] + direction[d];
                        } else if(dp_tmp[i + dy[d]][j + dx[d]].length() == t){
                            String new_path = dp[i][j] + direction[d];
                            dp_tmp[i + dy[d]][j + dx[d]] = (new_path.compareTo(dp_tmp[i + dy[d]][j + dx[d]]) < 0) ? new_path : dp_tmp[i + dy[d]][j + dx[d]];
                        }
                    }
                }
            }
            
            // array deep copy
            for(int i = 1; i <= n; i++){
                for(int j = 1; j <= m; j++){
                    if(dp_tmp[i][j] != null) dp[i][j] = new String(dp_tmp[i][j]);
                    else dp[i][j] = null;
                }
            }
        }        
                
        return (dp[r][c] == null || dp[r][c].isEmpty() || dp[r][c].length() != k) ? "impossible" : dp[r][c];
    }
}
```  
  
# 7. 1,2,3 떨어트리기  
그래프를 인접리스트로 구현한 후, 최대 떨어뜨릴 수 있는 횟수가 (1) 이상, (target의 모든 원소 합) 이하이므로, 각 drop마다 모든 node들을 검사하여 방정식의 해가 있는지만 검토하면 된다. 이때 사전순에서 가장 빠른 해를 원하기 때문에 1을 가장 많이 사용하는 것부터 검증해야 한다.  
  
단순한 문제인데 Lv.4에 정답률이 8%라 좀 당황했다. 개인적으로 2번이나 5번이 훨씬 어려웠다.  
  
```Java
import java.util.*;

class Solution {
    public int[] solution(int[][] edges, int[] target) {
        List<Integer>[] graph = new List[edges.length + 2]; // 1 - based
        for(int i = 1; i < graph.length; i++) graph[i] = new ArrayList<>();
        
        for(int i = 0; i < edges.length; i++){
            int[] e = edges[i];
            graph[e[0]].add(e[1]);
        }
        
        for(int i = 1; i < graph.length; i++) Collections.sort(graph[i]);
        
        int target_sum = 0;
        for(int i = 0; i < target.length; i++) target_sum += target[i];
        
        // selected_edge[i] = k : i -> graph[i].get(k) selected
        int[] selected_edge = new int[edges.length + 2];
        for(int i = 1; i < selected_edge.length; i++) {
            if(graph[i].size() == 0) selected_edge[i] = -1; // leaf
            selected_edge[i] = 0;
        }
        
        // simuation
        int[] drop_count_arr = new int[edges.length + 2];
        List<Integer> drop_order = new ArrayList<>();
        outer:
        for(int drop_count = 1; drop_count <= target_sum; drop_count++){
            // drop
            int root = 1;
            List<Integer> passed_vertex = new ArrayList<>();
            while(graph[root].size() != 0){
                passed_vertex.add(root);
                root = graph[root].get(selected_edge[root]);
            } // now root is leaf
            
            // change selected edge of passed_vertex(no leaf)
            for(int i = 0; i < passed_vertex.size(); i++){
                int v = passed_vertex.get(i);
                selected_edge[v] = (selected_edge[v] + 1) % graph[v].size();
            }
            
            // drop count
            drop_count_arr[root]++;
            drop_order.add(root);
            // 4          8         7         9       4     10        7 : drop_order
            // [1,1,0]  [1,0,0]  [0,1,1]  [0,1,0]   pass  [0,0,1]   pass : solution
            // 1 1 2 2 2 3 3(앞 인덱스)
            
            // check if exists solution
            int[][] solution = new int[edges.length + 2][4];
            
            inner: // at "i" vertex
            for(int i = 1; i < drop_count_arr.length; i++){
                if(target[i - 1] == 0 && drop_count_arr[i] != 0) return new int[]{-1};
                if(target[i - 1] == 0) continue;
                
                // target[i - 1] != 0
                // x + 2y + 3z = target[i - 1]
                // x + y + z = drop_count_arr[i]
                // y + 2z = target[i - 1] - drop_count_arr[i]
                for(int x1 = drop_count_arr[i]; x1 >= 0; x1--){
                    // y + z = drop_count_arr[i] - x
                    // 2y + 3z = target[i - 1] - x
                    // z = target[i - 1] - 2 * drop_count_arr[i] + x
                    // y = 3 * drop_count_arr[i] - 2 * x - target[i - 1]
                    int y1 = 3 * drop_count_arr[i] - 2 * x1 - target[i - 1];
                    int z1 = target[i - 1] - 2 * drop_count_arr[i] + x1;
                    
                    if(y1 >= 0 && z1 >= 0) {
                        solution[i][0] = x1; solution[i][1] = y1; solution[i][2] = z1; solution[i][3] = 1;
                        continue inner;
                    }
                }
                
                // no solution exists
                continue outer;
            }
            
            // solution exists
            List<Integer> sol = new ArrayList<>();
            for(int i = 0; i < drop_order.size(); i++){
                int droped_v = drop_order.get(i);
                
                if(solution[droped_v][0] != 0) {
                    sol.add(1);
                    solution[droped_v][0]--;
                } else if(solution[droped_v][1] != 0){
                    sol.add(2);
                    solution[droped_v][1]--;
                } else {
                    sol.add(3);
                    solution[droped_v][2]--;
                }
            }
            
            return sol.stream().mapToInt(Integer::intValue).toArray();
        }
        
        return new int[]{-1};
    }
}
```  
  
확실히 인턴십 문제들보다는 어려웠다. 문제를 풀다보니 5시간동안 집중한다는 것이 체력적으로 엄청 힘든 일이었던 것 같다... 그리고 풀다보니 아이디어는 맞는데 구현 디테일과 디버깅에서 시간을 잡아먹는게 시간 소모가 큰 요인이었던 것 같다. 앞으로 구현 쪽 문제들을 더 연습해서 실수 없이 페이스 최대한 유지할 수 있도록 연습해야겠다.