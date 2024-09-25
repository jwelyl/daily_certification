# 24_09_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25682 **체스판 다시 칠하기 2**

[](https://www.acmicpc.net/problem/25682)

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
	private static int K;
	
	private static char[][] chess;
	private static int[][][] dp;	//	dp[0/1][r][c] : (1, 1)을 검정색/흰색으로 칠했을 때, (1, 1), (r, c) 부분 직사각형에서 다시 칠해야 하는 개수
	
	private static int ans = Integer.MAX_VALUE;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		chess = new char[N][M];
		dp = new int[2][N + 1][M + 1];
		
		for(int r = 0; r < N; r++)
			chess[r] = br.readLine().toCharArray();
		
		//	(1, 1)이 검정색일 경우, r + c 합이 짝수인 칸은 검정색이어야 함
		//	(1, 1)이 흰색일 경우, r + c 합이 짝수인 칸은 흰색이어야 함
		for(int r = 1; r <= N; r++) {
			for(int c = 1; c <= M; c++) {
				if(chess[r - 1][c - 1] == 'B') {	//	(r, c)가 검정일 경우
					if((r + c) % 2 == 0) {	//	dp[0]일 경우, 검정색이 있어야 하고, dp[1]일 경우, 흰색이 있어야 함
						dp[0][r][c] = dp[0][r - 1][c] + dp[0][r][c - 1] - dp[0][r - 1][c - 1];		//	원래 검정색이 있어야 할 칸이므로 변경 필요 없음
						dp[1][r][c] = dp[1][r - 1][c] + dp[1][r][c - 1] - dp[1][r - 1][c - 1] + 1;	//	원래 흰색이 있어야 할 칸이므로 변경 필요
					}
					else {		//	dp[0]일 경우, 흰색이 있어야 하고, dp[1]일 경우, 검정색이 있어야 함
						dp[0][r][c] = dp[0][r - 1][c] + dp[0][r][c - 1] - dp[0][r - 1][c - 1] + 1;	//	원래 흰색이 있어야 할 칸이므로 변경 필요
						dp[1][r][c] = dp[1][r - 1][c] + dp[1][r][c - 1] - dp[1][r - 1][c - 1];		//	원래 검정색이 있어야 할 칸이므로 변경 필요 없음
					}
				}
				else {	//	(r, c)가 흰색일 경우
					if((r + c) % 2 == 0) {	//	dp[0]일 경우, 검정색이 있어야 하고, dp[1]일 경우, 흰색이 있어야 함
						dp[0][r][c] = dp[0][r - 1][c] + dp[0][r][c - 1] - dp[0][r - 1][c - 1] + 1;	//	원래 검정색이 있어야 할 칸이므로 변경 필요
						dp[1][r][c] = dp[1][r - 1][c] + dp[1][r][c - 1] - dp[1][r - 1][c - 1];		//	원래 흰색이 있어야 할 칸이므로 변경 필요 없음
					}
					else {		//	dp[0]일 경우, 흰색이 있어야 하고, dp[1]일 경우, 검정색이 있어야 함
						dp[0][r][c] = dp[0][r - 1][c] + dp[0][r][c - 1] - dp[0][r - 1][c - 1];		//	원래 흰색이 있어야 할 칸이므로 변경 필요 없음
						dp[1][r][c] = dp[1][r - 1][c] + dp[1][r][c - 1] - dp[1][r - 1][c - 1] + 1;	//	원래 검정색이 있어야 할 칸이므로 변경 필요
					}
				}
			}
		}

		for(int r = 1; r <= N - K + 1; r++) {
			for(int c = 1; c <= M - K + 1; c++) {
				int black = 0;	//	(r, c)를 검정색으로 칠할 경우, (r, c), (r + K - 1, c + K - 1)에서 다시 칠해야 하는 칸 수
				int white = 0;	//	(r, c)를 검정색으로 칠할 경우, (r, c), (r + K - 1, c + K - 1)에서 다시 칠해야 하는 칸 수
				
				if((r + c) % 2 == 0) {	//	(1, 1)을 칠한 것을 따라가면 됨
					black = dp[0][r + K - 1][c + K - 1] - dp[0][r - 1][c + K - 1] - dp[0][r + K - 1][c - 1] + dp[0][r - 1][c - 1];
					white = dp[1][r + K - 1][c + K - 1] - dp[1][r - 1][c + K - 1] - dp[1][r + K - 1][c - 1] + dp[1][r - 1][c - 1];
				}
				else {	//	(1, 1)을 칠한 것의 반대로 가면 됨
					black = dp[1][r + K - 1][c + K - 1] - dp[1][r - 1][c + K - 1] - dp[1][r + K - 1][c - 1] + dp[1][r - 1][c - 1];
					white = dp[0][r + K - 1][c + K - 1] - dp[0][r - 1][c + K - 1] - dp[0][r + K - 1][c - 1] + dp[0][r - 1][c - 1];
				}
				
				ans = Math.min(ans, Math.min(black, white));
			}
		}
		
		System.out.println(ans);
	} // main-end
} // Main-class-end
```