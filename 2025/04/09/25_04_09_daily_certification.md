# 25_04_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2410 2의 멱수의 합

[2410번: 2의 멱수의 합](http://boj.ma/2410/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MOD = 1_000_000_000;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static int[] dp;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		dp = new int[N + 1];
		dp[0] = 1;
		
		for(int i = 1; i <= N; i++) {
			dp[i] = dp[i - 1];
			
			if(i % 2 == 0)
				dp[i] = (dp[i] % MOD + dp[i / 2] % MOD) % MOD;
		}
		
		System.out.println(dp[N]);
	} //	main-end
} //	Main-class-end
```