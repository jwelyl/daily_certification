# 25_02_16_daily_certification

```
[#047 koreii] 데일리인증 20250216
1. 코딩 테스트 대비 알고리즘 학습
- DP (BOJ 2342 Dance Dance Revolution)
2. 자격증
- ADsP 데이터 분석 교재 학습 (통계, 회귀분석)
```

# Problem Solving (Algorithm & SQL)

### BOJ 1153 네 개의 소수

[1153번: 네 개의 소수](http://boj.ma/1153/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 100_000 * 4 + 1;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static final List<Integer> orders = new ArrayList<>();
    
    private static final int[][] force = {
    		{0, 2, 2, 2, 2},
    		{0, 1, 3, 4, 3},
    		{0, 3, 1, 3, 4},
    		{0, 4, 3, 1, 3},
    		{0, 3, 4, 3, 1}
    };
    
    private static int[][][] dp;	//	dp[i][l][r] : 왼쪽발이 l, 오른쪽발이 r에 있을때 i번까지 누르는데 최소 힘
    
    private static int answer = MAX;
    
    public static void main(String[] args) throws IOException {
    	orders.add(0);
    	
    	st = new StringTokenizer(br.readLine());
    	while(true) {
    		int pos = Integer.parseInt(st.nextToken());
    		
    		if(pos == 0)
    			break;
    		
    		orders.add(pos);
    	}
    	
    	dp = new int[orders.size()][5][5];
    	for(int i = 0; i < orders.size(); i++) {
    		for(int j = 0; j < 5; j++)
    			for(int k = 0; k < 5; k++)
    				dp[i][j][k] = MAX;
    	}
    	
    	dp[0][0][0] = 0;
    	
    	for(int i = 1; i < orders.size(); i++) {
    		int target = orders.get(i);	//	target을 밟아야함
    		
    		for(int l = 0; l < 5; l++) {
    			for(int r = 0; r < 5; r++)
    				dp[i][target][r] = Math.min(dp[i][target][r], dp[i - 1][l][r] + force[l][target]);
    		}
    		
    		for(int l = 0; l < 5; l++) {
    			for(int r = 0; r < 5; r++)
    				dp[i][l][target] = Math.min(dp[i][l][target], dp[i - 1][l][r] + force[r][target]);
    		}
    	}
    	
    	for(int l = 0; l < 5; l++) {
    		for(int r = 0; r < 5; r++)
    			answer = Math.min(answer, dp[orders.size() - 1][l][r]);
    	}
    	
    	System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```