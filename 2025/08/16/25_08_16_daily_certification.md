# 25_08_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2266 금고 테스트

[2266번: 금고 테스트](http://boj.ma/2266/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	private static final int MAX_N = 500;	//	건물 최대 높이 / 금고 최대 개수
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	//	dp[c][h] : c개의 금고로 h의 높이에서 테스트하기 위한 최소 테스트 횟수
	private static final int[][] dp = new int[MAX_N + 1][MAX_N + 1];

	private static int N;
	private static int K;
	
	public static void main(String[] args) throws IOException {
		init();
		
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		System.out.println(dp[K][N]);
	}	//	main-end
	
	private static void init() {
		for(int c = 0; c <= MAX_N; c++)
			Arrays.fill(dp[c], INF);
		
		//	건물 높이가 1층일 경우 금고가 몇개건 1층에서 한번만 테스트
		for(int c = 1; c <= MAX_N; c++)
			dp[c][1] = 1;
		//	금고가 1개일 경우 무조건 1층부터 h층까지 h번 테스트해야 함
		for(int h = 1; h <= MAX_N; h++)
			dp[1][h] = h;
		
		for(int c = 2; c <= MAX_N; c++) {
			for(int h = 2; h <= MAX_N; h++) {	//	c개의 금고로 높이 h인 건물에서 테스트	
				for(int f = 1; f < h; f++) {	//	f층에서 금고를 하나 떨어뜨려보기
					//	1. f층에서 금고를 떨어뜨렸을때부숴질 경우(1)
					//	c - 1개의 금고로 1 ~ f - 1층에서 테스트(dp[c - 1][f - 1])	
					int case1 = 1 + dp[c - 1][f - 1];
					//	2. f층에서 금고를 떨어뜨렸을때부숴지지 않을 경우(1)
					//	c개의 금고로 f + 1 ~ h층(h - f)에서 테스트(dp[c][h])
					int case2 = 1 + dp[c][h - f];
					
					//	f층에서 테스트할 경우에 최악의 경우
					int casef = Math.max(case1, case2);
					
					dp[c][h] = Math.min(dp[c][h], casef);
				}
			}
		}
	}
}	//	Main-class-end
```

### BOJ 1670 정상 회담 2

[1670번: 정상 회담 2](http://boj.ma/1670/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MAX = 10_000;
	private static final int MOD = 987_654_321;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static final int[] dp = new int[MAX + 1];
	
	private static int N;
	
	public static void main(String[] args) throws IOException {
		dp[2] = 1;
		dp[4] = 2;
		
		for(int i = 6; i <= MAX; i += 2) {
			dp[i] = (2 * dp[i - 2]) % MOD;
			
			for(int j = 2; j <= i - 4; j += 2) {
				int add = (int)(((long)dp[j] * dp[i - j - 2]) % MOD); 
				dp[i] = (dp[i] + add) % MOD;
			}
		}
		
		N = Integer.parseInt(br.readLine());
		
		System.out.println(dp[N]);
	} //	main-end
} //	Main-class-end
```