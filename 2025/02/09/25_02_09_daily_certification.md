# 25_02_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2662 기업투자

[2662번: 기업투자](http://boj.ma/2662/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;	//	투자할 총 금액
    private static int M;	//	투자할 기업 수
    
    private static int[][] profits;	//	profits[i][j] : j 기업에 i원을 투자했을때 이익
    private static int[][] dp;		//	dp[i][j] : 1 ~ j 기업까지 i원을 투자했을때 최대 이익
    
    private static int[][] prevs;		//	prevs[i][j] : 1 ~ j 기업까지 i원을 투자해서 최대 이익을 얻었을때, j 기업에 투자한 금액 
    private static int[] investments;	//	investments[i] : 최대 이익을 얻었을 때 i 기업에 투자한 금액
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        profits = new int[N + 1][M + 1];
        dp = new int[N + 1][M + 1];
        prevs = new int[N + 1][M + 1];
        investments = new int[M + 1];
        
        for(int i = 1; i <= N; i++) {
        	st = new StringTokenizer(br.readLine());
        	st.nextElement();
        	for(int j = 1; j <= M; j++)
        		profits[i][j] = Integer.parseInt(st.nextToken());
        }
        
        knapsack();
        
        sb.append(dp[N][M]).append("\n");
        for(int i = 1; i <= M; i++)
        	sb.append(investments[i]).append(" ");
        System.out.println(sb);
    }    //    main-end
    
    private static void knapsack() {
    	for(int i = 1; i <= N; i++) {
    		for(int j = 1; j <= M; j++) {
    			for(int k = 0; k <= i; k++) {
    				if(dp[i][j] < dp[i - k][j - 1] + profits[k][j]) {
    					dp[i][j] = dp[i - k][j - 1] + profits[k][j];	//	j-1번째 기업까지 i-k원을 투자하고 j번째 기업에 k원 투자
    					prevs[i][j] = k;
    				}
    			}
    		}
    	}
    	
    	int cy = N;
    	int cx = M;
    	while(cy > 0 && cx > 0) {
    		int cost = prevs[cy][cx];	//	1 ~ cx까지의 기업에 cy원을 투자해서 최대 이익을 얻었을때 cx 기업에 투자한 금액
    		investments[cx] = cost;
    		
    		cy -= cost;
    		cx--;			//	1 ~ (cx - 1)까지의 기업에 cy - cost를 투자해서 최대 이익을 얻음
    	}
    }
}    //    Main-class-end
```