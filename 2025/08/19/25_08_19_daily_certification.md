# 25_08_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2695 공

[2695번: 공](http://boj.ma/2695/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_B = 50;		//	유리공 최대 개수
	private static final int MAX_M = 1_000;		//	건물 최대 높이
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;

    //	dp[b][m] : 유리공 b개로 높이 m인 건물에서 테스트하기 위한 최소 횟수
    private static final int[][] dp = new int[MAX_B + 1][MAX_M + 1];
    
    private static int P;	//	테스트케이스 개수
    private static int B;	//	유리공 수
    private static int M;	//	건물 높이
    
    public static void main(String[] args) throws IOException {
    	for(int b = 0; b <= MAX_B; b++)
    		Arrays.fill(dp[b], Integer.MAX_VALUE);
    	
    	//	공이 1개면 무조건 1층부터 m층까지 차례로 테스트해야 함
    	for(int m = 1; m <= MAX_M; m++)
    		dp[1][m] = m;
    	//	공이 몇개건 1층 건물에서는 1층 한번만 테스트하면 됨
    	for(int b = 1; b <= MAX_B; b++)
    		dp[b][1] = 1;
    	
    	for(int b = 2; b <= MAX_B; b++) {	//	공이 b개 있을 경우
    		for(int m = 2; m <= MAX_M; m++) {	//	m층에서 공 b개로 테스트하기
    			for(int h = 1; h < m; h++) {	//	h층에서 떨어뜨다고 가정
    				//	h층에서 공을 떨어뜨렸을때(1) 공이 부숴진 경우, 1 ~ h - 1까지 층을 b - 1개의 공으로 테스트(dp[b - 1][h - 1])해야 함
    				int case1 = 1 + dp[b - 1][h - 1];
    				//	h층에서 공을 떨어뜨렸을때(1) 공이 부숴지지 않은 경우, h + 1층부터 m층까지 m - h개의 층을 b개의 공으로 테스트(dp[b][m - h])해야 함
    				int case2 = 1 + dp[b][m - h];
    			
    				dp[b][m] = Math.min(dp[b][m], Math.max(case1, case2));
    			}
    		}
    	}
    		
    	P = Integer.parseInt(br.readLine());
    	
    	for(int tc = 1; tc <= P; tc++) {
	    	st = new StringTokenizer(br.readLine());
	    	B = Integer.parseInt(st.nextToken());
	    	M = Integer.parseInt(st.nextToken());
	    	
	    	sb.append(dp[B][M]).append("\n");
    	}
    	
    	System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```