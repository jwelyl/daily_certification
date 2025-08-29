# 25_08_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10273 고대 동굴 탐사

[10273번: 고대 동굴 탐사](http://boj.ma/10273/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
import java.util.Stack;

public class Main {
    private static final int ROOT = 1;
    private static final int NONE = 0;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스
    
    private static int N;    //    동굴 수
    private static int E;    //    동굴 사이 통로 수
    
    //    values[v] : v번째 동굴 가치
    private static int[] values;
    private static List<int[]>[] graph;
    
    private static int[] parents;
    
    //    dp[v] : v번째 동굴까지 왔을떄 최대 가치
    private static int[] dp;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            st = new StringTokenizer(br.readLine());
            N = Integer.parseInt(st.nextToken());
            E = Integer.parseInt(st.nextToken());
            
            values = new int[N + 1];
            graph = new ArrayList[N + 1];
            
            parents = new int[N + 1];
            dp = new int[N + 1];
            
            st = new StringTokenizer(br.readLine());
            for(int v = 1; v <= N; v++) {
                values[v] = Integer.parseInt(st.nextToken());
                graph[v] = new ArrayList<>();
                dp[v] = Integer.MIN_VALUE;
            }
            
            for(int e = 0; e < E; e++) {
                st = new StringTokenizer(br.readLine());
                int v1 = Integer.parseInt(st.nextToken());
                int v2 = Integer.parseInt(st.nextToken());
                int c = Integer.parseInt(st.nextToken());
                
                graph[v1].add(new int[] {v2, c});
            }
            
            bfs();
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void bfs() {
        Queue<Integer> queue = new LinkedList<>();
        int maxProfit = 0;                        //    최대 가치
        int maxProfitV = NONE;                    //    최대 가치가 되는 도착 동굴
        Stack<Integer> path = new Stack<>();      //    그때 동굴 경로
        
        dp[ROOT] = values[ROOT];
        maxProfit = dp[ROOT];
        maxProfitV = ROOT;
        queue.offer(ROOT);
        
        while(!queue.isEmpty()) {
            int cur = queue.poll();
            
            for(int[] edge : graph[cur]) {
                int next = edge[0];
                int cost = edge[1];
                
                if(dp[cur] - cost + values[next] > dp[next]) {
                    dp[next] = dp[cur] - cost + values[next];
                    parents[next] = cur;
                    queue.offer(next);
                    
                    if(dp[next] > maxProfit) {
                        maxProfit = dp[next];
                        maxProfitV = next;
                    }
                }
            }
        }
        
        while(maxProfitV != NONE) {
            path.add(maxProfitV);
            maxProfitV = parents[maxProfitV];
        }
        
        sb.append(maxProfit).append(" ").append(path.size()).append("\n");
        while(!path.isEmpty())
            sb.append(path.pop()).append(" ");
        sb.append("\n");
    }
}    //    Main-class-end
```