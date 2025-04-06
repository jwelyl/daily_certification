# 25_04_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 21757 나누기

[21757번: 나누기](http://boj.ma/21757/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int[] nums;
	private static int sum = 0;
	
	private static long[][] dp;
	
	private static long answer = 0;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		nums = new int[N + 1];
		st = new StringTokenizer(br.readLine());
		for(int i = 1; i <= N; i++) {
			nums[i] = Integer.parseInt(st.nextToken());
			sum += nums[i];
		}
		
		if(sum % 4 == 0) {	//	4등분이 될 수도 있을 경우
			dp = new long[N + 1][4];
			
			int ps = 0;
			
			dp[0][0] = 1;

			for(int i = 1; i <= N; i++) {
				dp[i][0] = 1;
				ps += nums[i];
				
				for(int j = 1; j <= 3; j++) {
					dp[i][j] = dp[i - 1][j];
					
					if((sum / 4) * j == ps)
						dp[i][j] += dp[i - 1][j - 1];
				}
			}
			
			answer = dp[N - 1][3];
		}
			
		System.out.println(answer);
	}	// main-end
} // Main-class-end
```