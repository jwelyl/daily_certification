# 25_06_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2140 지뢰찾기

[2140번: 지뢰찾기](http://boj.ma/2140/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static char[][] board;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		board = new char[N][N];
		for(int y = 0; y < N; y++)
			board[y] = br.readLine().toCharArray();

		if(N <= 2) {
			System.out.println(0);
			return;
		}
		
		if(N > 4)
			answer += (N - 4) * (N - 4);
		
		for(int y = 0; y < N; y += N - 1) {
			for(int x = 0; x < N; x++) {
				int cnt = board[y][x] - '0';
				
				if(cnt == 0 || cnt == 3) {
					for(int d = 0; d < 8; d++) {
						int ny = y + dy[d];
						int nx = x + dx[d];
						
						if(isIn(ny, nx) && board[ny][nx] == '#') {
							if(cnt == 0)
								board[ny][nx] = 'X';
							else
								board[ny][nx] = '*';
						}
					}
				}
			}
		}
		
		for(int x = 0; x < N; x += N - 1) {
			for(int y = 1; y < N - 1; y++) {
				int cnt = board[y][x] - '0';
				
				if(cnt == 0 || cnt == 3) {
					for(int d = 0; d < 8; d++) {
						int ny = y + dy[d];
						int nx = x + dx[d];
						
						if(isIn(ny, nx) && board[ny][nx] == '#') {
							if(cnt == 0)
								board[ny][nx] = 'X';
							else
								board[ny][nx] = '*';
						}
					}
				}
			}
		}

		for(int x = 0; x < N; x++) {
			int cnt = board[0][x] - '0';
			
			if(1 <= cnt && cnt <= 2) {
				for(int d = 3; d >= 1; d--) {
					int ny = dy[d];
					int nx = x + dx[d];
					
					if(!isIn(ny, nx))
						continue;
					
					if(board[ny][nx] == '#') {
						if(cnt > 0) {
							cnt--;
							board[ny][nx] = '*';
						}
						else
							board[ny][nx] = 'X';
					}
					else if(board[ny][nx] == '*')
						cnt--;
				}
			}
		}
		for(int y = 1; y < N; y++) {
			int cnt = board[y][N - 1] - '0';
			
			if(1 <= cnt && cnt <= 2) {
				for(int d = 5; d >= 3; d--) {
					int ny = y + dy[d];
					int nx = N - 1 + dx[d];
					
					if(!isIn(ny, nx))
						continue;
					
					if(board[ny][nx] == '#') {
						if(cnt > 0) {
							cnt--;
							board[ny][nx] = '*';
						}
						else
							board[ny][nx] = 'X';
					}
					else if(board[ny][nx] == '*')
						cnt--;
				}
			}
		}
		for(int x = N - 2; x >= 0; x--) {
			int cnt = board[N - 1][x] - '0';
			
			if(1 <= cnt && cnt <= 2) {
				for(int d = 7; d >= 5; d--) {
					int ny = N - 1 + dy[d];
					int nx = x + dx[d];
					
					if(!isIn(ny, nx))
						continue;
					
					if(board[ny][nx] == '#') {
						if(cnt > 0) {
							cnt--;
							board[ny][nx] = '*';
						}
						else
							board[ny][nx] = 'X';
					}
					else if(board[ny][nx] == '*')
						cnt--;
				}
			}
		}
		for(int y = N - 2; y >= 1; y--) {
			int cnt = board[y][0] - '0';
			
			if(1 <= cnt && cnt <= 2) {
				for(int d = 1; d >= -1; d--) {
					int ny = y + dy[(d + 8) % 8];
					int nx = dx[(d + 8) % 8];
					
					if(!isIn(ny, nx))
						continue;
					
					if(board[ny][nx] == '#') {
						if(cnt > 0) {
							cnt--;
							board[ny][nx] = '*';
						}
						else
							board[ny][nx] = 'X';
					}
					else if(board[ny][nx] == '*')
						cnt--;
				}
			}
		}
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				if(board[y][x] == '*')
					answer++;
		}
		
		System.out.println(answer);
	}	// main-end
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```