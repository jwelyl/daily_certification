# 25_09_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23289 온풍기 안녕!

[23289번: 온풍기 안녕!](http://boj.ma/23289/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int TARGET = 5; // 온도 확인해야 하는 칸

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int R;
	private static int C;
	private static int K; // 목표 온도
	private static int W; // 벽 개수

	// map[y][x] : (y, x)칸의 온도, 초기에는 0임
	private static int[][] map;
	// blocked[d][y1][x1][y2][x2] : 온풍기가 d 방향으로 바람을 불 때 (y1, x1)에서 (y2, x2)로 바람이 퍼질
	// 수 있으면 false, 아니면 true
	private static boolean[][][][][] blocked;

	private static final List<int[]> checkPosList = new ArrayList<>();
	private static final List<Heater> heaterList = new ArrayList<>();

	private static int answer = 1;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());

		map = new int[R + 2][C + 2];
		blocked = new boolean[4][R + 2][C + 2][R + 2][C + 2];

		for (int y = 1; y <= R; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 1; x <= C; x++) {
				int num = Integer.parseInt(st.nextToken());

				if (1 <= num && num <= 4) // 온풍기일 경우
					heaterList.add(new Heater(y, x, num - 1));
				else if (num == TARGET) // 온도 체크해야 할 칸인 경우
					checkPosList.add(new int[] { y, x });
			}
		}

		W = Integer.parseInt(br.readLine());
		for (int w = 0; w < W; w++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			int type = Integer.parseInt(st.nextToken());

			if (type == 0) { // (y, x)와 (y - 1, x) 사이에 수평 벽 놓음
				blocked[0][y - 1][x][y][x + 1] = true;
				blocked[0][y][x][y - 1][x + 1] = true;

				blocked[1][y - 1][x][y][x - 1] = true;
				blocked[1][y][x][y - 1][x - 1] = true;

				blocked[2][y][x][y - 1][x] = true;
				blocked[2][y][x - 1][y - 1][x] = true;
				blocked[2][y][x + 1][y - 1][x] = true;

				blocked[3][y - 1][x][y][x] = true;
				blocked[3][y - 1][x - 1][y][x] = true;
				blocked[3][y - 1][x + 1][y][x] = true;
			} else if (type == 1) { // (y, x)와 (y, x + 1) 사이에 수직 벽 놓음
				blocked[0][y][x][y][x + 1] = true;
				blocked[0][y - 1][x][y][x + 1] = true;
				blocked[0][y + 1][x][y][x + 1] = true;

				blocked[1][y][x + 1][y][x] = true;
				blocked[1][y - 1][x + 1][y][x] = true;
				blocked[1][y + 1][x + 1][y][x] = true;

				blocked[2][y][x][y - 1][x + 1] = true;
				blocked[2][y][x + 1][y - 1][x] = true;

				blocked[3][y][x][y + 1][x + 1] = true;
				blocked[3][y][x + 1][y + 1][x] = true;
			}
		}

		for (; answer <= 100; answer++) {
			wind();
			adjust();
			decreaseOuter();

			if (isEnd())
				break;
		}

		System.out.println(answer);
	} // main-end

	// 집에 있는 모든 온풍기에서 바람이 한번씩 나옴
	private static void wind() {
		for (Heater heater : heaterList)
			bfs(heater);
	}

	private static void bfs(Heater heater) {
		Queue<int[]> queue = new LinkedList<>();
		boolean[][] visited = new boolean[R + 2][C + 2];
		int inc = 5;

		int sy = heater.y + dy[heater.dir];
		int sx = heater.x + dx[heater.dir];

		if (!isIn(sy, sx))
			return;

		visited[sy][sx] = true;
		map[sy][sx] += inc;
		queue.offer(new int[] { sy, sx });

		while (--inc > 0) {
			int size = queue.size(); // 증가 온도가 inc + 1일때 큐에 들어온 개수

			while (size-- > 0) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];

				for (int i = 0; i < 3; i++) {
					int ny = cy + dwy[heater.dir][i];
					int nx = cx + dwx[heater.dir][i];

					// (ny, nx)가 범위 안에 있으며, (cy, cx)로부터 바람을 받을 수 있고, 아직 바람 안받았으면
					if (isIn(ny, nx) && !blocked[heater.dir][cy][cx][ny][nx] && !visited[ny][nx]) {
						visited[ny][nx] = true;
						map[ny][nx] += inc;
						queue.offer(new int[] { ny, nx });
					}
				}
			}
		}
	}

	// 온도 조절
	private static void adjust() {
		int[][] adjusted = new int[R + 2][C + 2];

		for (int y = 1; y <= R; y++) {
			for (int x = 1; x <= C; x++) {
				// (y, x)는 이미 (y - 1, x)와 (y, x - 1)과 온도 교환을 한 상태임
				for (int d = 0; d < 4; d += 3) {
					int ny = y + dy[d];
					int nx = x + dx[d]; // d 방향으로 인접한 칸

					// (ny, nx)가 범위 안에 있고 d 방향으로 바람이 이동이 가능할때
					if (isIn(ny, nx) && !blocked[d][y][x][ny][nx]) {
						int diff = Math.abs(map[ny][nx] - map[y][x]) / 4;

						if (map[ny][nx] < map[y][x]) {
							adjusted[ny][nx] += diff;
							adjusted[y][x] -= diff;
						} else {
							adjusted[ny][nx] -= diff;
							adjusted[y][x] += diff;
						}
					}
				}
			}
		}

		// 모든 칸이 동시에 온도 교환한 것을 반영
		for (int y = 1; y <= R; y++) {
			for (int x = 1; x <= C; x++)
				map[y][x] += adjusted[y][x];
		}
	}

	// 온도가 1도 이상인 가장 바깥쪽 칸 온도 1씩 감소
	private static void decreaseOuter() {
		for (int x = 1; x <= C; x++) {
			if (map[1][x] >= 1)
				map[1][x]--;
			if (map[R][x] >= 1)
				map[R][x]--;
		}

		for (int y = 2; y < R; y++) {
			if (map[y][1] >= 1)
				map[y][1]--;
			if (map[y][C] >= 1)
				map[y][C]--;
		}
	}

	private static boolean isEnd() {
		for (int[] pos : checkPosList) {
			if (map[pos[0]][pos[1]] < K) // 아직 K도 미만일 경우
				return false;
		}

		return true;
	}

	private static final int[] dy = { 0, 0, -1, 1 };
	private static final int[] dx = { 1, -1, 0, 0 };

	// dwy/x[d] : 온풍기가 d 방향으로 바람이 불 때 바람 퍼지는 3방향
	private static final int[][] dwy = { { -1, 0, 1 }, { -1, 0, 1 }, { -1, -1, -1 }, { 1, 1, 1 } };
	private static final int[][] dwx = { { 1, 1, 1 }, { -1, -1, -1 }, { -1, 0, 1 }, { -1, 0, 1 } };

	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= R) && (1 <= x && x <= C);
	}

	// 온풍기
	private static class Heater {
		public final int y;
		public final int x; // 온풍기 위치
		public final int dir; // 온풍기 바람 방향

		public Heater(int y, int x, int dir) {
			this.y = y;
			this.x = x;
			this.dir = dir;
		}
	}
} // Main-class-end
```

### **Codetree 냉방 시스템**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/cooling-system/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int TARGET = 1; // 시원함 확인해야 하는 칸

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int R;
	private static int K; // 목표 시원함
	private static int W; // 벽 개수

	// map[y][x] : (y, x)칸의 시원함, 초기에는 0임
	private static int[][] map;
	// blocked[d][y1][x1][y2][x2] : 에어컨이 d 방향으로 바람을 불 때 (y1, x1)에서 (y2, x2)로 바람이 퍼질
	// 수 있으면 false, 아니면 true
	private static boolean[][][][][] blocked;

	private static final List<int[]> checkPosList = new ArrayList<>();
	private static final List<AC> acList = new ArrayList<>();

	private static int answer = -1;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		W = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());

		map = new int[R + 2][R + 2];
		blocked = new boolean[4][R + 2][R + 2][R + 2][R + 2];

		for (int y = 1; y <= R; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 1; x <= R; x++) {
				int num = Integer.parseInt(st.nextToken());

				if (2 <= num && num <= 5) // 에에컨일 경우
					acList.add(new AC(y, x, num - 2));
				else if (num == TARGET) // 시원함 체크해야 할 칸인 경우
					checkPosList.add(new int[] { y, x });
			}
		}

		for (int w = 0; w < W; w++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			int type = Integer.parseInt(st.nextToken());

			if (type == 0) { // (y, x)와 (y - 1, x) 사이에 수평 벽 놓음
				// 왼쪽
				blocked[0][y - 1][x][y][x - 1] = true;
				blocked[0][y][x][y - 1][x - 1] = true;
				// 위쪽
				blocked[1][y][x][y - 1][x] = true;
				blocked[1][y][x - 1][y - 1][x] = true;
				blocked[1][y][x + 1][y - 1][x] = true;
				// 오른쪽
				blocked[2][y - 1][x][y][x + 1] = true;
				blocked[2][y][x][y - 1][x + 1] = true;
				// 아래쪽
				blocked[3][y - 1][x][y][x] = true;
				blocked[3][y - 1][x - 1][y][x] = true;
				blocked[3][y - 1][x + 1][y][x] = true;
			} else if (type == 1) { // (y, x - 1)와 (y, x) 사이에 수직 벽 놓음
				x--; // x를 1 감소시키면 (y, x)와 (y, x + 1) 사이에 수직 벽 놓은 것과 같음
				// 왼쪽
				blocked[0][y][x + 1][y][x] = true;
				blocked[0][y - 1][x + 1][y][x] = true;
				blocked[0][y + 1][x + 1][y][x] = true;
				// 위쪽
				blocked[1][y][x][y - 1][x + 1] = true;
				blocked[1][y][x + 1][y - 1][x] = true;
				// 오른쪽
				blocked[2][y][x][y][x + 1] = true;
				blocked[2][y - 1][x][y][x + 1] = true;
				blocked[2][y + 1][x][y][x + 1] = true;
				// 아래쪽
				blocked[3][y][x][y + 1][x + 1] = true;
				blocked[3][y][x + 1][y + 1][x] = true;
			}
		}

		for (int time = 1; time <= 100; time++) {
			wind();
			adjust();
			decreaseOuter();

			if (isEnd()) {
				answer = time;
				break;
			}
		}

		System.out.println(answer);
	} // main-end

	// 집에 있는 모든 에어컨에서 바람이 한번씩 나옴
	private static void wind() {
		for (AC ac : acList)
			bfs(ac);
	}

	private static void bfs(AC heater) {
		Queue<int[]> queue = new LinkedList<>();
		boolean[][] visited = new boolean[R + 2][R + 2];
		int inc = 5;

		int sy = heater.y + dy[heater.dir];
		int sx = heater.x + dx[heater.dir];

		if (!isIn(sy, sx))
			return;

		visited[sy][sx] = true;
		map[sy][sx] += inc;
		queue.offer(new int[] { sy, sx });

		while (--inc > 0) {
			int size = queue.size(); // 증가 시원함이 inc + 1일때 큐에 들어온 개수

			while (size-- > 0) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];

				for (int i = 0; i < 3; i++) {
					int ny = cy + dwy[heater.dir][i];
					int nx = cx + dwx[heater.dir][i];

					// (ny, nx)가 범위 안에 있으며, (cy, cx)로부터 바람을 받을 수 있고, 아직 바람 안받았으면
					if (isIn(ny, nx) && !blocked[heater.dir][cy][cx][ny][nx] && !visited[ny][nx]) {
						visited[ny][nx] = true;
						map[ny][nx] += inc;
						queue.offer(new int[] { ny, nx });
					}
				}
			}
		}
	}

	// 시원함 조절
	private static void adjust() {
		int[][] adjusted = new int[R + 2][R + 2];

		for (int y = 1; y <= R; y++) {
			for (int x = 1; x <= R; x++) {
				// (y, x)는 이미 (y - 1, x)와 (y, x - 1)과 시원함 교환을 한 상태임
				for (int d = 0; d < 4; d += 3) {
					int ny = y + dy[d];
					int nx = x + dx[d]; // d 방향으로 인접한 칸

					// (ny, nx)가 범위 안에 있고 d 방향으로 바람이 이동이 가능할때
					if (isIn(ny, nx) && !blocked[d][y][x][ny][nx]) {
						int diff = Math.abs(map[ny][nx] - map[y][x]) / 4;

						if (map[ny][nx] < map[y][x]) {
							adjusted[ny][nx] += diff;
							adjusted[y][x] -= diff;
						} else {
							adjusted[ny][nx] -= diff;
							adjusted[y][x] += diff;
						}
					}
				}
			}
		}

		// 모든 칸이 동시에 시원함 교환한 것을 반영
		for (int y = 1; y <= R; y++) {
			for (int x = 1; x <= R; x++)
				map[y][x] += adjusted[y][x];
		}
	}

	// 시원함이 1 이상인 가장 바깥쪽 칸 시원함 1씩 감소
	private static void decreaseOuter() {
		for (int x = 1; x <= R; x++) {
			if (map[1][x] >= 1)
				map[1][x]--;
			if (map[R][x] >= 1)
				map[R][x]--;
		}

		for (int y = 2; y < R; y++) {
			if (map[y][1] >= 1)
				map[y][1]--;
			if (map[y][R] >= 1)
				map[y][R]--;
		}
	}

	private static boolean isEnd() {
		for (int[] pos : checkPosList) {
			if (map[pos[0]][pos[1]] < K) // 아직 K도 미만일 경우
				return false;
		}

		return true;
	}

	// 왼쪽, 위쪽, 오른쪽, 아래쪽
	private static final int[] dy = { 0, -1, 0, 1 };
	private static final int[] dx = { -1, 0, 1, 0 };

	// dwy/x[d] : 에어컨이 d 방향으로 바람이 불 때 바람 퍼지는 3방향
	private static final int[][] dwy = { { -1, 0, 1 }, { -1, -1, -1 }, { -1, 0, 1 }, { 1, 1, 1 } };
	private static final int[][] dwx = { { -1, -1, -1 }, { -1, 0, 1 }, { 1, 1, 1 }, { -1, 0, 1 } };

	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= R) && (1 <= x && x <= R);
	}

	// 에어컨
	private static class AC {
		public final int y;
		public final int x; // 에어컨 위치
		public final int dir; // 에어컨 바람 방향

		public AC(int y, int x, int dir) {
			this.y = y;
			this.x = x;
			this.dir = dir;
		}
	}
} // Main-class-end
```

### 물고기 종류 별 대어 찾기

[](https://school.programmers.co.kr/learn/courses/30/lessons/293261#)

```sql
select t1.id, t3.fish_name, t2.length
from
fish_info as t1
inner join
(
    select fish_type, max(length) as length 
    from fish_info
    group by fish_type
) as t2 on t1.fish_type = t2.fish_type and t1.length = t2.length
inner join
fish_name_info as t3
on t1.fish_type = t3.fish_type
order by t1.id;
```