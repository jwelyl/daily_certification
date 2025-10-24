# 25_10_24_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 모두 0으로 만들기

[](https://school.programmers.co.kr/learn/courses/30/lessons/76503)

```java
import java.util.List;
import java.util.ArrayList;

class Solution {
    private static final int ROOT = 0;
    
    private static int N;
    private static List<Integer>[] tree;
    
    private static long[] values;
    //  dp[v] : v 서브트리의 모든 자식들을 0으로 만들 때 필요한 총 연산 횟수
    private static long[] dp;
    
    public long solution(int[] a, int[][] edges) {
        N = a.length;
        dp = new long[N];
        tree = new ArrayList[N];
        values = new long[N];
        
        long total = 0;
        for (int v = 0; v < N; v++) {
            tree[v] = new ArrayList<>();
            values[v] = a[v];
            total += a[v];
        }
        
        if (total != 0) 
            return -1;
        
        for (int[] edge : edges) {
            int v1 = edge[0];
            int v2 = edge[1];
            tree[v1].add(v2);
            tree[v2].add(v1);
        }
        
        dfs(ROOT, -1);
        
        return values[ROOT] != 0 ? -1 : dp[ROOT];
    }
    
    private static void dfs(int cur, int parent) {
        for (int i = 0; i < tree[cur].size(); i++) {
            int child = tree[cur].get(i);
            
            if(child == parent)
                continue;
            
            dfs(child, cur);
            dp[cur] += dp[child];
            dp[cur] += Math.abs(values[child]);
            values[cur] += values[child];
        }
    }
}

```