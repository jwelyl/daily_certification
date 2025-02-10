# 25_02_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 7579 앱

[7579번: 앱](http://boj.ma/7579/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;               //    앱 개수
    private static int M;               //    앱을 비활성화시켜서 확보해야 하는 메모리
    private static int[] memory;        //    memory[i] : i번째 앱을 비활성화시켰을때 얻을 수 있는 메모리
    private static int[] cost;          //    cost[i] : i번째 앱을 비활성화시키는 비용
    
    private static int maxCost = 0;     //    모든 앱을 비활성화시킨다고 가정할때 필요한 비용    
    private static int[][] dp;          //    dp[i][j] : 1번 앱부터 i번째 앱까지 총 비용 j 내에서 비활성화시켜서 얻을 수 있는 최대 메모리 
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        memory = new int[N + 1];
        cost = new int[N + 1];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            memory[i] = Integer.parseInt(st.nextToken());
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            cost[i] = Integer.parseInt(st.nextToken());
            maxCost += cost[i];
        }
        
        dp = new int[N + 1][maxCost + 1];
        
        knapsack();
    }    //    main-end
    
    private static void knapsack() {
        int minCost = maxCost;
        
        for(int i = 1; i <= N; i++) {
            for(int j = 0; j <= maxCost; j++) {
                if(j < cost[i])    //    i번째 앱을 비활성화시키는 비용이 j보다 클 경우, i번째 앱을 비활성화시킬 수 없음
                    dp[i][j] = dp[i - 1][j];    //    j의 비용으로 1번 앱부터 (i-1)번째 앱까지 비활성시켜서 얻을 수 있는 최대 메모리 
                else    //    i번째 앱을 비활성화시켜서 메모리 얻고, 1번 앱부터 (i-1)번째 앱까지 j-cost[i]의 비용으로 비활성시켜서 얻을 수 있는 최대 메모리를 합함
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - cost[i]] + memory[i]);    //    두 값을 비교함
            
                if(dp[i][j] >= M && j < minCost)
                    minCost = j;
            }
        }
        
        System.out.println(minCost);
    }
}    //    Main-class-end
```

### BOJ 17404 RGB거리 2

[17404번: RGB거리 2](http://boj.ma/17404/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final int R = 0;
    private static final int G = 1;
    private static final int B = 2;
    
    private static final int INF = 1_000 * 1_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;           //    집 개수
    private static int[][] cost;    //    cost[i][j] : i번째 집을 j색으로 칠하는 비용
    
    private static int[][] dp;          //    dp[i][j] : 조건을 모두 만족하면서 1번 집부터 i번째 집까지 칠하고 i번째 집의 색을 j로 칠했을 때 최소 비용
    private static int answer = INF;    //    N개의 집을 모두 칠하는데 필요한 비용
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        cost = new int[N + 1][3];
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 0; j < 3; j++)
                cost[i][j] = Integer.parseInt(st.nextToken());
        }
        
        dp = new int[N + 1][3];
        
        for(int start = 0; start < 3; start++) {    //    1번 집을 start로 칠하고 시작
            Arrays.fill(dp[1], INF);
            dp[1][start] = cost[1][start];
            
            for(int i = 2; i < N; i++) {    //    2번 집부터 N-1번 집까지 칠하기
                Arrays.fill(dp[i], INF);
                
                dp[i][R] = Math.min(dp[i - 1][G], dp[i - 1][B]) + cost[i][R];
                dp[i][G] = Math.min(dp[i - 1][B], dp[i - 1][R]) + cost[i][G];
                dp[i][B] = Math.min(dp[i - 1][R], dp[i - 1][G]) + cost[i][B];
            }
            
            Arrays.fill(dp[N], INF);
            //    N번 집은 1번 집과 달라야 하므로 start로 칠할 수 없음
            dp[N][R] = (start == R) ? INF : Math.min(dp[N - 1][G], dp[N - 1][B]) + cost[N][R];
            dp[N][G] = (start == G) ? INF : Math.min(dp[N - 1][B], dp[N - 1][R]) + cost[N][G];
            dp[N][B] = (start == B) ? INF : Math.min(dp[N - 1][R], dp[N - 1][G]) + cost[N][B];
            
            answer = Math.min(Math.min(answer, dp[N][R]), Math.min(dp[N][G], dp[N][B]));
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```