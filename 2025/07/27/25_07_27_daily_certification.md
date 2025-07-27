# 25_07_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6079 Checkers

[6079번: Checkers](http://boj.ma/6079/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class Main {
	private static final char K = 'K';
	private static final char O = 'o';
	private static final char E = '+';

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();

	private static int N;
	private static char[][] board;

	private static final List<int[]> kings = new ArrayList<>(); //
	private static int oCnt = 0; // 잡아야 할 적 말의 개수

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		board = new char[N][N];

		for (int y = 0; y < N; y++) {
			board[y] = br.readLine().toCharArray();
			for (int x = 0; x < N; x++) {
				if (board[y][x] == K)
					kings.add(new int[] { y, x });
				else if (board[y][x] == O)
					oCnt++;
			}
		}
		
		for(int[] king : kings)
			backtracking(king[0], king[1], oCnt, new Stack<>());
		
		//	하나의 킹으로 한번에 잡을 수 없을 경우
		System.out.println("impossible");
	} // main-end
	
	private static void backtracking(int ky, int kx, int remainCnt, Stack<int[]> pathStack) {
		board[ky][kx] = K;
		pathStack.push(new int[] {ky + 1, kx + 1});
		
		if(remainCnt == 0) {	//	K 하나로 한번에 모든 적의 말을 토벌했을 경우
			for(int[] pos : pathStack)
				sb.append(pos[0]).append(" ").append(pos[1]).append("\n");
			
			System.out.print(sb);
			System.exit(0);
		}
		
		for(int d = 0; d < 4; d++) {
			if(canJump(ky, kx, d)) {	//	(ky, kx)에서 d 방향으로 점프할 수 있으면
				board[ky][kx] = E;
				board[ky + dy[d]][kx + dx[d]] = E;
				backtracking(ky + 2 * dy[d], kx + 2 * dx[d], remainCnt - 1, pathStack);
				board[ky + dy[d]][kx + dx[d]] = O;
				board[ky][kx] = K;
			}
		}
		
		pathStack.pop();
		board[ky][kx] = E;
	}

	private static final int[] dy = { 1, 1, -1, -1 };
	private static final int[] dx = { 1, -1, -1, 1 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}

	// (y, x)에 있는 K가 d 방향으로 점프할 수 있나 확인
	private static boolean canJump(int y, int x, int d) {
		return isIn(y + dy[d], x + dx[d]) && board[y + dy[d]][x + dx[d]] == O && 
				isIn(y + 2 * dy[d], x + 2 * dx[d]) && board[y + 2 * dy[d]][x + 2 * dx[d]] == E;
	}
} // Main-class-end
```