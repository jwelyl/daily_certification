# 24_07_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2615 오목

[](https://www.acmicpc.net/problem/2615)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;
	private static final int MAX = 19;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static final int[][] board = new int[MAX][MAX];
	
	public static void main(String[] args) throws IOException {
		for(int r = 0; r < MAX; r++) {
			tokens = new StringTokenizer(br.readLine());
			for(int c = 0; c < MAX; c++)
				board[r][c] = Integer.parseInt(tokens.nextToken());
		}
		
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++) {
				if(board[r][c] == NONE)	//	돌이 놓여있지 않으면 skip
					continue;

				boolean win = false;	//	현재 돌로 이긴 경우 true
				
				for(int d = 0; d < 4; d++) {	//	현재 돌을 가장 왼쪽에 위치한 돌로 놓고 4방향 확인
					int br = r - dr[d];
					int bc = c - dc[d];	//	현재 방향에서 현재 돌의 이전 돌 위치
					
					if(isIn(br, bc) && board[br][bc] == board[r][c])	//	이전 돌이 현재 돌과 같을 경우, 현재 돌은 이미 지난번에 확인했음
						continue;
					
					int cnt = 1;	//	현재 돌과 같은 색 연속한 개수(현재 돌 포함 1)
					
					for(int k = 1; k <= 4; k++) {
						int nr = r + dr[d] * k;
						int nc = c + dc[d] * k;	//	현재 방향에서 다음 돌 위치
						
						if(!isIn(nr, nc) || board[nr][nc] != board[r][c])	//	다음 돌 위치가 범위 밖이거나, 다음 돌이 현재 돌과 색이 다를 경우
							break;
						
						cnt++;
					}
					
					if(cnt != 5)	//	 현재 방향으로는 같은 색 돌이 연속해서 5개가 존재하지 않을 경우
						continue;
					
					//	현재 방향으로 6번째 돌 위치가 범위 밖이거나, 현재 돌과 색이 다를 경우
					if(!isIn(r + dr[d] * 5, c + dc[d] * 5) || board[r + dr[d] * 5][c + dc[d] * 5] != board[r][c]) {
						win = true;
						break;
					}
				}
				
				if(win) {
					System.out.println(board[r][c]);
					System.out.println((r + 1) + " " + (c + 1));
					return;
				}
			}
		}
		
		System.out.println(0);
	}	//	main-end
	
	private static final int[] dr = {-1, 0, 1, 1};
	private static final int[] dc = {1, 1, 1, 0};
	
	private static boolean isIn(int r, int c) {
		return (0 <= r && r < MAX) && (0 <= c && c < MAX);
	}
}	//	Main-class-end
```
