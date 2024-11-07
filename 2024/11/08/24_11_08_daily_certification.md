# 24_11_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1949 우수 마을

[](https://www.acmicpc.net/problem/1949)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    
    private static int[] cnts;
    private static List<Integer>[] tree;
    private static boolean[] visited;
    private static int[][] dp;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        cnts = new int[N + 1];
        tree = new ArrayList[N + 1];
        
        st = new StringTokenizer(br.readLine());
        for(int v = 1; v <= N; v++) {
            cnts[v] = Integer.parseInt(st.nextToken());
            tree[v] = new ArrayList<>();
        }
        visited = new boolean[N + 1];
        dp = new int[N + 1][2];
        
        for(int m = 0; m < N - 1; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            tree[v1].add(v2);
            tree[v2].add(v1);
        }
        
        dfs(1);
        
        System.out.println(Math.max(dp[1][0], dp[1][1]));
    }    //    main-end
    
    private static void dfs(int v) {
        visited[v] = true;
        dp[v][1] = cnts[v];    //    v 마을의 주민 수를 포함
        
        for(int next : tree[v]) {
            if(!visited[next]) {    //    next가 v의 자식일 경우
                dfs(next);    //    next 탐색
                
                //    v가 우수마을이 아닐 경우, next는 우수마을일수도, 아닐수도 있음
                dp[v][0] += Math.max(dp[next][0], dp[next][1]);
                //    v가 우수마을일 경우, next는 우수마을일수 없음
                dp[v][1] += dp[next][0];
            }
        }
    }
}    //    Main-class-end
```