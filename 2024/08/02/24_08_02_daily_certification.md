# 24_08_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18430 무기 공학

[](https://www.acmicpc.net/problem/18430)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;				//	행 개수
	private static int M;				//	열 개수
	
	private static int[][] board;
	private static boolean[][] visited;
	
	private static int maxStr = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		board = new int[N][M];
		visited = new boolean[N][M];
		for(int r = 0; r < N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < M; c++)
				board[r][c] = Integer.parseInt(st.nextToken());
		}
	
		dfs(0, 0);
	
		System.out.println(maxStr);
	}	//	main-end
	
	private static void dfs(int num, int str) {
		if(num == N * M) {	//	모든 칸을 다 확인했을 경우
			maxStr = Math.max(maxStr, str);
			return;
		}
		
		int y0 = num / M;
		int x0 = num % M;
		
		if(!visited[y0][x0]) {	//	(y0, x0)에 부메랑이 놓이지 않은 경우, 부메랑 놓아보기
			for(int d = 0; d < 4; d++) {	//	d번째 부메랑 놓아보기
				int y1 = y0 + dy[0][d];
				int x1 = x0 + dx[0][d];
				int y2 = y0 + dy[1][d];
				int x2 = x0 + dx[1][d];
				
				if(isIn(y1, x1) && isIn(y2, x2) && !visited[y1][x1] && !visited[y2][x2]) {	//	d번째 부메랑을 놓을 수 있을 경우
					visited[y0][x0] = true;
					visited[y1][x1] = true;
					visited[y2][x2] = true;	//	(y0, x0)에 부메랑 놓은 것 표시
					
					dfs(num + 1, str + 2 * board[y0][x0] + board[y1][x1] + board[y2][x2]);	//	d번째 부메랑을 (y0, x0)에 놓은 상태로 다음 칸으로 이동
					
					visited[y0][x0] = false;
					visited[y1][x1] = false;
					visited[y2][x2] = false;	//	(y0, x0)에 부메랑 놓은 것 표시 해제
				}
			}
		}
	
		dfs(num + 1, str);	//	(y0, x0)에 부메랑 놓지 않고 다음 칸 이동
	}
	
	private static final int[][] dy = {{0, 0, 0, 0}, {1, -1, -1, 1}};
	private static final int[][] dx = {{-1, -1, 1, 1}, {0, 0, 0, 0}};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```