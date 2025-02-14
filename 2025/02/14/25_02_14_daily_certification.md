# 25_02_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3114 사과와 바나나

[3114번: 사과와 바나나](http://boj.ma/3114/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;

	private static int[][] bananas;	//	bananas[y][x] : 좌상단 : (1, 1), 우하단 : (y, x)인 직사각형 영역의 바나나 누적합
	private static int[][] apples;	//	apples[y][x] : 좌상단 : (1, 1), 우하단 : (y, x)인 직사각형 영역의 사과 누적합
	
	private static int[][] dp;	//	dp[y][x] : (y, x)까지 길을 냈을때 위쪽 바나나 합과 아래쪽 사과 합의 최댓값
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		bananas = new int[N + 1][M + 1];
		apples = new int[N + 1][M + 1];
		dp = new int[N + 1][M + 1];
		
		for(int y = 1; y <= N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 1; x <= M; x++) {
				String input = st.nextToken();
				char fruit = input.charAt(0);
				int cnt = Integer.parseInt(input.substring(1));
				
				bananas[y][x] = bananas[y - 1][x] + bananas[y][x - 1] - bananas[y - 1][x - 1];
				apples[y][x] = apples[y - 1][x] + apples[y][x - 1] - apples[y - 1][x - 1];
				
				if(fruit == 'B')	//	바나나일 경우
					bananas[y][x] += cnt;
				else				//	사과일 경우
					apples[y][x] += cnt;
			}
		}

		dp[1][1] = partialSum(2, 1, N, 1, apples);
		
		for(int x = 2; x <= M; x++)
			dp[1][x] = dp[1][x - 1] + partialSum(2, x, N, x, apples);
		for(int y = 2; y <= N; y++)
			dp[y][1] = dp[y - 1][1] - partialSum(y, 1, y, 1, apples);
		
		for(int y = 2; y <= N; y++) {
			for(int x = 2; x <= M; x++) {
				//	좌상단에서 이동했을 경우
				dp[y][x] = Math.max(dp[y][x], dp[y - 1][x - 1] + partialSum(1, x, y - 1, x, bananas) + partialSum(y + 1, x, N, x, apples));
				//	왼쪽에서 이동했을 경우
				dp[y][x] = Math.max(dp[y][x], dp[y][x - 1] + partialSum(1, x, y - 1, x, bananas) + partialSum(y + 1, x, N, x, apples));
				//	위에서 이동했을 경우
				dp[y][x] = Math.max(dp[y][x], dp[y - 1][x] - partialSum(y, x, y, x, apples));
			}
		}
		
		System.out.println(dp[N][M]);
	} //	main-end
	
	//	prefixSum을 이용하여 좌상단 : (y1, x1), 우하단 : (y2, x2)인 직사각형 영역의 부분합 구함
	private static int partialSum(int y1, int x1, int y2, int x2, int[][] prefixSum) {
		return prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1];
	}
} //	Main-class-end
```

### BOJ 2662 기업투자

[2662번: 기업투자](http://boj.ma/2662/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	투자 금액
	private static int M;	//	투자 가능한 기업 개수
	
	private static int[][] profits;	//	profits[i][j] : i 기업에 j원 투자했을때 이익
	private static int[][] dp;		//	dp[i][j] : 1~i 기업에 총 j원을 투자했을때 얻을 수 
	
	private static int[][] investments;	//	investments[i][j] : 1~i 기업에 총 j원을 투자해서 최대 이익을 얻었을때 j 기업에 투자한 금액
	private static final Stack<Integer> stack = new Stack<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		profits = new int[M + 1][N + 1];
		dp = new int[M + 1][N + 1];
		investments = new int[M + 1][N + 1];
		
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			st.nextToken();
			
			for(int j = 1; j <= M; j++)
				profits[j][i] = Integer.parseInt(st.nextToken());
		}
		
		for(int i = 1; i <= M; i++) {
			for(int j = 1 ; j <= N; j++) {
				for(int k = 0; k <= j; k++) {
					int profit = Math.max(dp[i][j], dp[i - 1][j - k] + profits[i][k]);	//	i 기업에 k원을 투자했을때 얻을 수 있는 이익
					
					//	i 기업에 k원을 투자하는게 최대일 경우
					if(dp[i][j] < profit) {
						dp[i][j] = profit;
						investments[i][j] = k;
					}
				}
			}
		}
		
		sb.append(dp[M][N]).append("\n");
		
		int y = M;
		int x = N;
		
		while(y > 0) {
			int investment = investments[y][x];
			stack.push(investment);	//	총 x원 투자했을때 y 기업에 investment만큼 투자하는게 최대
			y--;
			x -= investment;	//	1~(y-1) 기업에 x - investment만큼 투자하는게 최대
		}
		
		while(!stack.isEmpty())
			sb.append(stack.pop()).append(" ");
		
		System.out.println(sb);
	} //	main-end
} //	Main-class-end
```