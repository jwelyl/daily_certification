# 25_06_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1035 조각 움직이기

[1035번: 조각 움직이기](http://boj.ma/1035/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Queue;
import java.util.Set;

public class Main {
	private static final int SIZE = 5;

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static final Set<Integer> endSet = new HashSet<>();
	private static final Set<Integer> visited = new HashSet<>();

	private static int cnt = 0;
	private static final char[][] initBoard = new char[5][];
	private static int initStatus;

	public static void main(String[] args) throws IOException {
		for (int y = 0; y < SIZE; y++) {
			initBoard[y] = br.readLine().toCharArray();
			for (int x = 0; x < SIZE; x++) {
				if (initBoard[y][x] == '*') {
					initBoard[y][x] = '1';
					cnt++;
				} else
					initBoard[y][x] = '0';
			}
		}

		initStatus = getStatus(initBoard);

		combination(0, 0, new int[cnt]);

		System.out.println(bfs());
	} // main-end

	private static final int[] comps = { 0, 4, 0, 4 };
	private static final int[] adds = { -1, 1, -5, 5 };

	private static int bfs() {
		if (endSet.contains(initStatus))
			return 0;

		Queue<int[]> queue = new LinkedList<>();
		visited.add(initStatus);
		queue.offer(new int[] { initStatus, 0 });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cstatus = cur[0];
			int ccnt = cur[1];

			for (int idx = 0; idx < SIZE * SIZE; idx++) {
				int mask = (1 << idx);

				if ((cstatus & mask) != 0) { // idx번째 칸에 조각상이 있을 경우
					for (int d = 0; d < 4; d++) {
						int comp = (d == 0 || d == 1) ? idx % SIZE : idx / SIZE;

						if (comp != comps[d]) {
							int nstatus = cstatus;
							int nidx = idx + adds[d];
							int nmask = (1 << nidx);

							if ((cstatus & nmask) == 0) { // 이동할 칸에 다른 조각상 없을 경우
								// 이동한 후 nidx의 비트는 1, idx의 비트는 0
								nstatus ^= (1 << nidx) | (1 << idx);

								if (endSet.contains(nstatus))
									return ccnt + 1;

								if (!visited.contains(nstatus)) {
									visited.add(nstatus);
									queue.offer(new int[] { nstatus, ccnt + 1 });
								}
							}
						}
					}
				}
			}
		}

		return -1;
	}

	private static void combination(int nth, int start, int[] selected) {
		if (nth == cnt) { // cnt개를 모두 뽑았을 경우
			char[][] board = new char[5][5];
			for (int y = 0; y < 5; y++) {
				for (int x = 0; x < 5; x++)
					board[y][x] = '0';
			}

			for (int num : selected) {
				int y = num / SIZE;
				int x = num % SIZE;

				board[y][x] = '1';
			}

			int call = 0;
			boolean[][] visited = new boolean[SIZE][SIZE];

			for (int y = 0; y < SIZE; y++) {
				for (int x = 0; x < SIZE; x++) {
					if (board[y][x] == '1' && !visited[y][x]) {
						call++;
						bfs(y, x, board, visited);
					}
				}
			}

			if (call == 1)
				endSet.add(getStatus(board));

			return;
		}

		for (int num = start; num < SIZE * SIZE; num++) {
			selected[nth] = num;
			combination(nth + 1, num + 1, selected);
		}
	}

	private static void bfs(int y, int x, char[][] board, boolean[][] visited) {
		Queue<int[]> queue = new LinkedList<>();
		visited[y][x] = true;
		queue.offer(new int[] { y, x });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];

			for (int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];

				if (isIn(ny, nx) && board[ny][nx] == '1' && !visited[ny][nx]) {
					visited[ny][nx] = true;
					queue.offer(new int[] { ny, nx });
				}
			}
		}
	}

	private static int getStatus(char[][] status) {
		StringBuilder sb = new StringBuilder();

		for (int y = 0; y < SIZE; y++)
			sb.append(new String(status[y]));

		return Integer.parseInt(sb.toString(), 2);
	}

	private static final int[] dy = { 0, 1, 0, -1 };
	private static final int[] dx = { 1, 0, -1, 0 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < SIZE) && (0 <= x && x < SIZE);
	}
} // Main-class-end
```