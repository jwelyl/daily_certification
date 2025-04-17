# 25_04_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2266 **금고 테스트**

[2266번: 금고 테스트](http://boj.ma/2266/t)

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
	private static int M;

	//	dp[c][h] : c개의 금고로 h의 높이에서 테스트하기 위한 최소 테스트 횟수
	private static int[][] dp;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		dp = new int[M + 1][N + 1];
		for (int c = 0; c <= M; c++)
			Arrays.fill(dp[c], Integer.MAX_VALUE);
			
		for (int h = 0; h <= N; h++)
			dp[1][h] = h;
		for (int c = 0; c <= M; c++)
			dp[c][1] = 1;

		for (int c = 2; c <= M; c++) {
			for (int h = 2; h <= N; h++) {
				for (int k = 1; k < h; k++) {
					//	k에서 떨어뜨렸을떄 금고가 부숴진 경우 1 ~ k-1까지 k - 1개 층을 c - 1개의 금고로 테스트해야 함
					int case1 = dp[c - 1][k - 1] + 1;
					//	k에서 떨어뜨렸을때 금고가 부숴지지 않은 경우 k+1 ~h까지 h - k개 층을 c개의 금고로 테스트해야 함
					int case2 = dp[c][h - k] + 1;
					
					dp[c][h] = Math.min(dp[c][h], Math.max(case1, case2));
				}
			}
		}

		System.out.println(dp[M][N]);
	}	//	main-end
}	//	Main-class-end
```