# 25_03_01_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 인사고과

[](https://school.programmers.co.kr/learn/courses/30/lessons/152995#)

```java
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;

class Solution {
    private int score0 = 0;
    private int score1 = 0; //  완호의 점수
    
    private int higher = 0; //  점수 총합이 완호 점수 총합보다 높은 사원 수
    
    private final List<int[]> filtered = new ArrayList<>(); //  인센티브를 받을 수 없는 사원을 제외한 리스트
    
    public int solution(int[][] scores) {
        score0 = scores[0][0];
        score1 = scores[0][1];
        
        //  근무 태도 점수가 높은 순으로, 근무 태도 점수가 같을 경우, 동료 평가 점수가 낮은 순으로 정렬
        Arrays.sort(scores, (score1, score2) -> score1[0] == score2[0] ? (score1[1] - score2[1]) : (score2[0] - score1[0]));
        
        filtered.add(scores[0]);
        
        for(int i = 1; i < scores.length; i++) {
            int[] last = filtered.get(filtered.size() - 1);
            
            //  인센티브를 받은 사원과 근무 태도 점수가 같거나, 근무 태도 점수가 낮아도 동료 평가 점수가 더 높을 경우
            if(last[0] == scores[i][0] || last[1] <= scores[i][1])
                filtered.add(scores[i]);
            else {  //  위에 해당하지 않을 경우, 
                if(scores[i][0] == score0 && scores[i][1] == score1)    //  완호의 점수가 
                    return -1;
            }
        }
        
        //  점수 총합이 높은 순으로 정렬
        Collections.sort(filtered, (score1, score2) -> Integer.compare(score2[0] + score2[1], score1[0] + score1[1]));
        
        for(int[] score : filtered) {
            if(score[0] + score[1] > score0 + score1)   //  완호의 점수 총합보다 높을 경우
                higher++;
        }
        
        return higher + 1;
    }
    
    private void print() {
        System.out.print("filtered : ");
        for(int[] score : filtered)
            System.out.print(print(score) + ", ");
        System.out.println();  
    }
    
    private String print(int[] arr) {
        return "[" + arr[0] + ", " + arr[1] + "]";
    }
}
```

### 프로그래머스 숫자 카드 나누기

[](https://school.programmers.co.kr/learn/courses/30/lessons/135807)

```java
class Solution {
    public int solution(int[] arrayA, int[] arrayB) {
        int answer = 0;

        int gcdA = gcd(arrayA); //  A 배열의 수를 모두 나눌 수 있는 최대 수
        int gcdB = gcd(arrayB); //  B 배열의 수를 모두 나눌 수 있는 최대 수
        
        //  A 배열의 수를 모두 나눌 수 있는 수들에 대하여
        for(int a = 1; a * a <= gcdA; a++) {
            if(gcdA % a == 0) { //  gcdA가 a로 나누어 떨어질 경우
                int left = a;
                int right = gcdA / a;
                
                boolean leftOk = true;
                boolean rightOk = true;
                
                for(int i = 0; i < arrayB.length; i++) {
                    if(arrayB[i] % left == 0) {
                        leftOk = false;
                        break;
                    }
                }
                
                for(int i = 0; i < arrayB.length; i++) {
                    if(arrayB[i] % right == 0) {
                        rightOk = false;
                        break;
                    }
                }

                if(leftOk)  //  left로 A의 모든 수를 나눌 수 있으면서 B의 모든 수는 나눌 수 없음
                    answer = Math.max(answer, left);
                if(rightOk)  //  right로 A의 모든 수를 나눌 수 있으면서 B의 모든 수는 나눌 수 없음
                    answer = Math.max(answer, right);
            }
        }

        //  B 배열의 수를 모두 나눌 수 있는 수들에 대하여
        for(int a = 1; a * a <= gcdB; a++) {
            if(gcdA % a == 0) { //  gcdB가 a로 나누어 떨어질 경우
                int left = a;
                int right = gcdB / a;
                
                boolean leftOk = true;
                boolean rightOk = true;
                
                for(int i = 0; i < arrayA.length; i++) {
                    if(arrayA[i] % left == 0) {
                        leftOk = false;
                        break;
                    }
                }
                
                for(int i = 0; i < arrayA.length; i++) {
                    if(arrayA[i] % right == 0) {
                        rightOk = false;
                        break;
                    }
                }
                
                if(leftOk)   //  left로 B의 모든 수를 나눌 수 있으면서 A의 모든 수는 나눌 수 없음
                    answer = Math.max(answer, left);
                if(rightOk)  //  right로 B의 모든 수를 나눌 수 있으면서 A의 모든 수는 나눌 수 없음
                    answer = Math.max(answer, right);
            }
        }
        
        return answer;
    }
    
    private int gcd(int[] array) {
        int gcd = array[0];
        
        for(int i = 1; i < array.length; i++)
            gcd = gcd(gcd, array[i]);
        
        return gcd;
    }
    
    private int gcd(int num1, int num2) {
        int big = num1 > num2 ? num1 : num2;
        int small = big == num1 ? num2 : num1;
        
        while(true) {
            int r = big % small;
            
            if(r == 0)
                break;
            
            big = small;
            small = r;
        }
        
        return small;
    }
}
```

### 프로그래머스 등대

[](https://school.programmers.co.kr/learn/courses/30/lessons/133500)

```java
import java.util.List;
import java.util.ArrayList;

class Solution {
    private final int ROOT = 1;
    
    private List<Integer>[] graph;
    private List<Integer>[] tree;
    
    //  간선의 두 정점 중 하나라도 불이 들어오면 해당 간선엔 불이 들어온 것
    //  dp[v][0] : v의 불을 껐을때 v를 루트로 하는 서브트리에 포함된 간선에 불이 모두 들어오기 위해 불을 켜야 하는 정점 최소 개수
    //  dp[v][1] : v의 불을 켰을때 v를 루트로 하는 서브트리에 포함된 간선에 불이 모두 들어오기 위해 불을 켜야 하는 정점 최소 개수
    private int[][] dp;
    
    public int solution(int n, int[][] lighthouse) {
        graph = new ArrayList[n + 1];
        tree = new ArrayList[n + 1];
        dp = new int[n + 1][2];
        
        for(int v = 0; v <= n; v++) {
            graph[v] = new ArrayList<>();
            tree[v] = new ArrayList<>();
        }
        
        for(int[] edge : lighthouse) {
            int v1 = edge[0];
            int v2 = edge[1];
            
            graph[v1].add(v2);
            graph[v2].add(v1);
        }
        
        //  ROOT를 정점으로 하는 트리 생성
        makeTree(ROOT, new boolean[n + 1]);
        
        dfs(ROOT);
        
        return Math.min(dp[ROOT][0], dp[ROOT][1]);
    }
    
    private void dfs(int root) {
        if(tree[root].size() == 0) {    //  root가 리프노드일 경우
            dp[root][0] = 0;
            dp[root][1] = 1;
        }
        else {
            for(int child : tree[root]) {
                dfs(child);
                
                //  root의 불을 끄려면 자식들은 반드시 불이 켜져 있어야 함
                dp[root][0] += dp[child][1];
                //  root의 불을 킬 경우 자식들은 불이 켜져있든 꺼져 있든 상관 없음
                dp[root][1] += Math.min(dp[child][0], dp[child][1]);
            }
            
            dp[root][1]++;  //  root의 불을 켬
        }
    }
    
    private void makeTree(int root, boolean[] visited) {
        visited[root] = true;
        
        for(int next : graph[root]) {
            if(!visited[next]) {
                tree[root].add(next);
                makeTree(next, visited);
            }
        }
    }
}
```