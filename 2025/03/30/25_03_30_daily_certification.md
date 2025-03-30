# 25_03_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6144 Charm Bracelet

[6144번: Charm Bracelet](http://boj.ma/6144/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static int[] w;
	private static int[] d;
	private static int[][] dp;
	
    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	N = Integer.parseInt(st.nextToken());
    	M = Integer.parseInt(st.nextToken());
    	
    	w = new int[N + 1];
    	d = new int[N + 1];
    	
    	dp = new int[N + 1][M + 1];
    	
    	for(int i = 1; i <= N; i++) {
    		st = new StringTokenizer(br.readLine());
        	w[i] = Integer.parseInt(st.nextToken());
        	d[i] = Integer.parseInt(st.nextToken());
    	}
    	
    	for(int i = 1; i <= N; i++) {
    		for(int j = 1; j <= M; j++) {
    			if(w[i] > j)
    				dp[i][j] = dp[i - 1][j];
    			else
    				dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - w[i]] + d[i]);
    		}
    	}
    	
    	System.out.println(dp[N][M]);
    }    //    main-end
}    //    Main-class-end
```