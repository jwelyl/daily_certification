# 25_02_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2624 동전 바꿔주기

[2624번: 동전 바꿔주기](http://boj.ma/2624/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int T;
	private static int K;
	private static int[][] coins;	//	coins[i] : i번째 동전의 {가격, 개수}

	private static int[] dp;		//	dp[i] : 금액 i를 동전으로 교환하는 경우의 수
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		K = Integer.parseInt(br.readLine());
		
		coins = new int[K + 1][2];
		dp = new int[T + 1];
		
		for(int k = 1; k <= K; k++) {
			st = new StringTokenizer(br.readLine());
		
			coins[k][0] = Integer.parseInt(st.nextToken());
			coins[k][1] = Integer.parseInt(st.nextToken());
		}
		
		dp[0] = 1;	//	0을 만드는 방법 = 어떤 동전도 사용하지 않기
		
		for(int k = 1; k <= K; k++) {
			int val = coins[k][0];	//	동전 가격
			int cnt = coins[k][1];	//	동전 개수
			
			for(int t = T; t >= val; t--) {	//	val보다 작은 가격은 k번째 동전이 개입할 수 없음
				for(int i = 1; i <= cnt; i++) {	//	k번째 동전 개수
					if(t - val * i < 0)
						break;
					
					dp[t] += dp[t - val * i];	//	t를 만드는 방법 += t - val * i를 만드는 방법
				}
			}
		}
		
		System.out.println(dp[T]);
	} //	main-end
} //	Main-class-end
```

### BOJ 24427 알고리즘 수업 - 행렬 경로 문제 4

[24427번: 알고리즘 수업 - 행렬 경로 문제 4](http://boj.ma/24427/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int P;
    private static int[][] arr;
    
    private static boolean[][] passed; 	// 	반드시 지나야 하는 점을 포함한 경로 여부
    private static long[][] dp1;		//	반드시 지나야 하는 점 없이 구한 값
    private static long[][] dp2;		//	반드시 지나야 하는 점을 고려한 값

    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());

        arr = new int[N + 1][N + 1];
        passed = new boolean[N + 1][N + 1];
        dp1 = new long[N + 1][N + 1];
        dp2 = new long[N + 1][N + 1];

        for (int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for (int j = 1; j <= N; j++)
                arr[i][j] = Integer.parseInt(st.nextToken());
        }

        for(int y = 1; y <= N; y++) {
			for(int x = 1; x <= N; x++)
				dp1[y][x] = Math.max(dp1[y - 1][x], dp1[y][x - 1]) + arr[y][x];
		}
		
		P = Integer.parseInt(br.readLine());
		for(int i = 0; i < P; i++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			
			dp2[y][x] = dp1[y][x];
			passed[y][x] = true;
		}

		for(int y = 1; y <= N; y++) {
			for(int x = 1; x <= N; x++) {
				if(passed[y - 1][x]) {	//	위쪽에서 (y, x)로 올 수 있는 경우
					dp2[y][x] = Math.max(dp2[y][x], dp2[y - 1][x] + arr[y][x]);
					passed[y][x] = true;
  				}
				if(passed[y][x - 1]) {	//	왼쪽에서 (y, x)로 올 수 있는 경우
					dp2[y][x] = Math.max(dp2[y][x], dp2[y][x - 1] + arr[y][x]);
					passed[y][x] = true;
				}
			}
		}
		
		System.out.println(dp2[N][N]);
    }	//	main-end
}	//	Main-class-end
```

### BOJ 4811 알약

[4811번: 알약](http://boj.ma/4811/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	
	private static int N;

	private static long[][] dp;	//	dp[y][x] : 한 조각 알약 개수 y개, 반 조각 알약 개수 x개인 경우의 수

	public static void main(String[] args) throws IOException {
		while(true) {
			N = Integer.parseInt(br.readLine());
			
			if(N == 0) {
				System.out.print(sb);
				break;
			}
			
			dp = new long[N + 1][N + 1];
			dp[N][0] = 1;
			
			for(int y = N; y >= 0; y--) {	//	y : 한 조각 알약 개수
				int start = N - y;
				
				for(int x = start; x >= 0; x--) {	//	x : 반 조각 알약 개수
					if(y > 0)
						dp[y - 1][x + 1] += dp[y][x];
					if(x > 0)
						dp[y][x - 1] += dp[y][x];
				}
			}
			
			sb.append(dp[0][0]).append("\n");
		}
	} //	main-end
} //	Main-class-end
```

[Pagination이란 (페이징 처리) - Offset vs Cursor](https://betterdev.tistory.com/17)