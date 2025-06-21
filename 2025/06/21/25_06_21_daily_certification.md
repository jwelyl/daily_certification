# 25_06_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2098 외판원 순회

[](http://boj.ma/2098/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final int UNDEFINED = -1;
	private static final int NONE = 1_000_000 * 20;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	
	private static int[][] W;
	//	dp[now][status] : 현재 now 정점이고 방문 상태가 status일때 최소 비용
	private static int[][] dp;
	
	private static int endStatus;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		endStatus = (1 << N) - 1;
		W = new int[N + 1][N + 1];
		dp = new int[N + 1][1 << N];
	
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			for(int j = 1; j <= N; j++)
				W[i][j] = Integer.parseInt(st.nextToken());
		}
		
		for(int i = 0; i <= N; i++)
			Arrays.fill(dp[i], UNDEFINED);

		System.out.println(tsp(1, 1));
	} // main-end
	
	private static int tsp(int now, int status) {
		if(status == endStatus) {	//	모든 도시를 다 방문한 경우
			if(W[now][1] == 0)	//	now에서 1로 갈 수 없을 경우
				return NONE;
			 
			return W[now][1];	//	now에서 1로 가는 비용 반환	
		}
		
		if(dp[now][status] != UNDEFINED)
			return dp[now][status];
		
		dp[now][status] = NONE;
		
		for(int v = 1; v <= N; v++) {
			int nStatus = status | (1 << (v - 1));
			
			if(W[now][v] == 0 || (status & (1 << (v - 1))) != 0)
				continue;
			
			dp[now][status] = Math.min(dp[now][status], tsp(v, nStatus) + W[now][v]);
		}
		
		return dp[now][status];
	}
} // Main-class-end
```

### BOJ 16991 외판원 순회 3

[](http://boj.ma/16991/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final double UNDEFINED = -1.0;
	private static final double NONE = 3_000.0 * 20;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	
	private static int[] xpos;
	private static int[] ypos;
	
	private static double[][] W;
	//	dp[now][status] : 현재 now 정점이고 방문 상태가 status일때 최소 비용
	private static double[][] dp;
	
	private static int endStatus;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		endStatus = (1 << N) - 1;
		
		xpos = new int[N + 1];
		ypos = new int[N + 1];
		
		W = new double[N + 1][N + 1];
		dp = new double[N + 1][1 << N];
	
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			xpos[i] = Integer.parseInt(st.nextToken());
			ypos[i] = Integer.parseInt(st.nextToken());
		}
		
		for(int i = 1; i < N; i++) {
			for(int j = i + 1; j <= N; j++) {
				double dist = Math.sqrt((xpos[i] - xpos[j]) * (xpos[i] - xpos[j]) + (ypos[i] - ypos[j]) * (ypos[i] - ypos[j]));
				W[i][j] = dist;
				W[j][i] = dist;
			}
		}
		
		for(int i = 0; i <= N; i++)
			Arrays.fill(dp[i], UNDEFINED);

		System.out.println(tsp(1, 1));
	} // main-end
	
	private static double tsp(int now, int status) {
		if(status == endStatus) {	//	모든 도시를 다 방문한 경우
			if(W[now][1] == 0)	//	now에서 1로 갈 수 없을 경우
				return NONE;
			 
			return W[now][1];	//	now에서 1로 가는 비용 반환	
		}
		
		if(dp[now][status] != UNDEFINED)
			return dp[now][status];
		
		dp[now][status] = NONE;
		
		for(int v = 1; v <= N; v++) {
			int nStatus = status | (1 << (v - 1));
			
			if(W[now][v] == 0 || (status & (1 << (v - 1))) != 0)
				continue;
			
			dp[now][status] = Math.min(dp[now][status], tsp(v, nStatus) + W[now][v]);
		}
		
		return dp[now][status];
	}
} // Main-class-end
```