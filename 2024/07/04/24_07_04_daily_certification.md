# 24_07_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4920 테트리스 게임

[](https://www.acmicpc.net/problem/4920)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MIN_VALUE;
	private static final boolean T = true;
	private static final boolean F = false;

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;

	private static int TC = 0;
	private static int N;
	private static int[][] board;
	private static int maxSum = NONE;

	private static final boolean[][][][] tetriminos = {
			// 0.
			{
					// 회전 상태 0
					{ { T, T, T, T }, { F, F, F, F }, { F, F, F, F }, { F, F, F, F } },
					// 회전 상태 1
					{ { T, F, F, F }, { T, F, F, F }, { T, F, F, F }, { T, F, F, F } } },
			// 1.
			{
					// 회전 상태 0
					{ { T, T, F, F }, { F, T, T, F }, { F, F, F, F }, { F, F, F, F } },
					// 회전 상태 1
					{ { F, T, F, F }, { T, T, F, F }, { T, F, F, F }, { F, F, F, F } } },
			// 2.
			{
					// 회전 상태 0
					{ { T, T, T, F }, { F, F, T, F }, { F, F, F, F }, { F, F, F, F } },
					// 회전 상태 1
					{ { T, T, F, F }, { T, F, F, F }, { T, F, F, F }, { F, F, F, F } },
					// 회전 상태 2
					{ { T, F, F, F }, { T, T, T, F }, { F, F, F, F }, { F, F, F, F } },
					// 회전 상태 3
					{ { F, T, F, F }, { F, T, F, F }, { T, T, F, F }, { F, F, F, F } } },
			// 3.
			{
					// 회전 상태 0
					{ { T, T, T, F }, { F, T, F, F }, { F, F, F, F }, { F, F, F, F } },
					// 회전 상태 1
					{ { T, F, F, F }, { T, T, F, F }, { T, F, F, F }, { F, F, F, F } },
					// 회전 상태 2
					{ { F, T, F, F }, { T, T, T, F }, { F, F, F, F }, { F, F, F, F } },
					// 회전 상태 3
					{ { F, T, F, F }, { T, T, F, F }, { F, T, F, F }, { F, F, F, F } } },
			// 4.
			{
					// 회전 상태 0
					{ { T, T, F, F }, { T, T, F, F }, { F, F, F, F }, { F, F, F, F } } } };

	public static void main(String[] args) throws IOException {
		while (true) {
			N = Integer.parseInt(br.readLine().trim());

			if (N == 0) {
				System.out.print(sb);
				break;
			}
			
			board = new int[N][N];
			maxSum = NONE;
			
			for(int i = 0; i < N; i++) {
				tokens = new StringTokenizer(br.readLine());
				for(int j = 0; j < N; j++)
					board[i][j] = Integer.parseInt(tokens.nextToken());
			}
			
			simulation();

			sb.append(++TC).append(". ").append(maxSum).append("\n");
		}
	} // main-end
	
	private static void simulation() {
		for(int t = 0; t < tetriminos.length; t++) {	//	t번째 테트리미노를
			for(int r = 0; r < tetriminos[t].length; r++) {	//	r번째 회전 상태로
				boolean[][] tetrimino = tetriminos[t][r];
				
				for(int row = 0; row < N; row++) {
					for(int col = 0; col < N; col++)	//	(row, col)에 놓았을 때 점수
						maxSum = Math.max(maxSum, put(row, col, tetrimino));
				}
			}
		}
	}
	
	//	board의 (row, col)에 tetrimino를 놓을 수 있을 경우 해당 점수 반환
	//	놓을 수 없을 경우 NONE 반환
	private static int put(int row, int col, boolean[][] tetrimino) {
		int ret = 0;
		
		for(int r = 0; r < 4; r++) {
			for(int c = 0; c < 4; c++) {
				if(tetrimino[r][c]) {	//	(r, c)가 블록일 경우
					if(!isIn(row + r, col + c))	//	대응되는 board의 칸이 범위 밖일 경우
						return NONE;	//	(row, col)에 tetrimino 놓을 수 없음
					
					ret += board[row + r][col + c];
				}
			}
		}
		
		return ret;
	}
	
	private static boolean isIn(int row, int col) {
		return (0 <= row && row < N) && (0 <= col && col < N);
	}
} // Main-class-end
```