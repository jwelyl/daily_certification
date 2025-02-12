# 25_02_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24426 알고리즘 수업 - 행렬 경로 문제 3

[24426번: 알고리즘 수업 - 행렬 경로 문제 3](http://boj.ma/24426/t)

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
	private static int[][] arr;
	private static long[][] dp;
	
	private static int midY;
	private static int midX;			//	거쳐가거나 피할 위치 (midY, midX)
	private static long answer1;		//	(1, 1)에서 (midY, midX)를 거쳐서 (N, N)에 도착했을때 최댓값
	private static long answer2;		//	(1, 1)에서 (midY, midX)를 거치지 않고 (N, N)에 도착했을때 최댓값

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		arr = new int[N + 1][N + 1];
		dp = new long[N + 1][N + 1];
	
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			for(int j = 1; j <= N; j++)
				arr[i][j] = Integer.parseInt(st.nextToken());
		}
		
		st = new StringTokenizer(br.readLine());
		midY = Integer.parseInt(st.nextToken());
		midX = Integer.parseInt(st.nextToken());
	
		answer1 = dp(midY, midX);
		
		arr[midY][midX] = Integer.MIN_VALUE;
		
		answer2 = dp(N, N);
		
		System.out.println(answer1 + " " + answer2);
	}	//	main-end
	
	//	dp 배열 초기화
	private static void initDp() {
		for(int y = 0; y <= N; y++)
			Arrays.fill(dp[y], 0);
	}
	
	//	(1, 1)에서 (my, mx)를 거쳐서 (N, N)으로 갔을때 최댓값
	private static long dp(int my, int mx) {
		initDp();
		
		for(int x = 1; x <= mx; x++)
			dp[1][x] = dp[1][x - 1] + arr[1][x];
		for(int y = 1; y <= my; y++)
			dp[y][1] = dp[y - 1][1] + arr[y][1];
		
		//	(1, 1)에서 (my, mx)까지 최댓값으로 감
		for(int y = 2; y <= my; y++) {
			for(int x = 2; x <= mx; x++)
				dp[y][x] = Math.max(dp[y - 1][x], dp[y][x - 1]) + arr[y][x];
		}

		for(int x = mx + 1; x <= N; x++)
			dp[my][x] = dp[my][x - 1] + arr[my][x];
		for(int y = my + 1; y <= N; y++)
			dp[y][mx] = dp[y - 1][mx] + arr[y][mx];
		
		//	(my, mx)에서 (N, N)까지 최댓값으로 감
		for(int y = my + 1; y <= N; y++) {
			for(int x = mx + 1; x <= N; x++)
				dp[y][x] = Math.max(dp[y - 1][x], dp[y][x - 1]) + arr[y][x];
		}
		
		return dp[N][N];
	}
	
	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= N) && (1 <= x && x <= N);
	}
}	//	Main-class-end
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