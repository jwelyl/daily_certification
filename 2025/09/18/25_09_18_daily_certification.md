# 25_09_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9347 울타리

[9347번: 울타리](http://boj.ma/9347/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	동전 종류
	private static int[][] coins;	//	coins[nth] : nth번째 동전의 {가치, 개수}
	private static int[] dp;		//	dp[value] : value를 만들 수 있는 경우의 수
	
	private static int sum = 0;	//	모든 동전 가치 합
	
	public static void main(String[] args) throws IOException {
		for(int tc = 1; tc <= 3; tc++) {
			N = Integer.parseInt(br.readLine());
			coins = new int[N + 1][2];
			sum = 0;
			
			for(int nth = 1; nth <= N; nth++) {
				st = new StringTokenizer(br.readLine());
				coins[nth][0] = Integer.parseInt(st.nextToken());
				coins[nth][1] = Integer.parseInt(st.nextToken());
				
				sum += coins[nth][0] * coins[nth][1];
			}
			
			if(sum % 2 == 1) {	//	동전 가치 합이 홀수라 절반으로 나눌 수 없을 경우
				sb.append(0).append("\n");
				continue;
			}
			
			dp = new int[sum / 2 + 1];
			Arrays.fill(dp, NONE);
			dp[0] = 0;
			
			for(int nth = 1; nth <= N; nth++) {
				int value = coins[nth][0];
				int cnt = coins[nth][1];
				
				for(int prevValue = 0; prevValue <= sum / 2; prevValue++) {
					if(dp[prevValue] == NONE)
						continue;
					
					if(prevValue + value <= sum / 2 && dp[prevValue] < cnt)
						dp[prevValue + value] = Math.min(dp[prevValue + value], dp[prevValue] + 1);
					
					dp[prevValue] = 0;
				}
			}
			
			sb.append(dp[sum / 2] == NONE ? 0 : 1).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
} //	Main-class-end
```