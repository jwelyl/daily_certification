# 24_08_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17085 십자가 2개 놓기

[](https://www.acmicpc.net/problem/17085)

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
	
	private static char[][] board;
	
	private static int maxMult = 1;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		board = new char[N][M];
		for(int r = 0; r < N; r++)
			board[r] = br.readLine().toCharArray();
		
		dfs(0, 1, 0);
		
		System.out.println(maxMult);
	}	//	main-end
	
	private static void dfs(int num, int mult, int cnt) {
		if(cnt == 2) {	//	2개 십자가를 모두 놓은 경우
			maxMult = Math.max(maxMult, mult);
			return;	//	십자가 2개 놓았으니 더 이상 놓을 수 없음
		}
		
		if(num == N * M)	//	모든 칸 다 확인했을 경우
			return;
		
		int y = num / M;
		int x = num % M;	//	십자가 중심의 좌표
		int size = 0;		//	(y, x)를 중심으로 놓을 수 있는 십자가의 최대 크기
		
		if(board[y][x] == '#') {	//	십자가를 놓을 수 있을 경우
			for(; ; size++) {
				int y0 = y + dy[0] * size;
				int x0 = x + dx[0] * size;
				int y1 = y + dy[1] * size;
				int x1 = x + dx[1] * size;
				int y2 = y + dy[2] * size;
				int x2 = x + dx[2] * size;
				int y3 = y + dy[3] * size;
				int x3 = x + dx[3] * size;
			
				if(canPut(y0, x0) && canPut(y1, x1) && canPut(y2, x2) && canPut(y3, x3)) {
					board[y][x] = '*';
					board[y0][x0] = '*';
					board[y1][x1] = '*';
					board[y2][x2] = '*';
					board[y3][x3] = '*';	//	size만큼 십자가 놓아보기
					
					dfs(num + 1, mult * (1 + 4 * size), cnt + 1);
				}
				else break;
			}
			
			size--;
			
			for(; size >= 0; size--) {
				int y0 = y + dy[0] * size;
				int x0 = x + dx[0] * size;
				int y1 = y + dy[1] * size;
				int x1 = x + dx[1] * size;
				int y2 = y + dy[2] * size;
				int x2 = x + dx[2] * size;
				int y3 = y + dy[3] * size;
				int x3 = x + dx[3] * size;
				
				board[y][x] = '#';
				board[y0][x0] = '#';
				board[y1][x1] = '#';
				board[y2][x2] = '#';
				board[y3][x3] = '#';	//	size만큼 십자가 놓았던 것 취소
			}
		}
		
		dfs(num + 1, mult, cnt);	//	십자가를 놓지 않고 다음 칸으로 가보기
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean canPut(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M) && board[y][x] == '#';
	}
}	//	Main-class-end
```