# 25_09_10_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 미지의 공간 탈출**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/escape-unknown-space/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	private static final int EMPTY = 0;
	private static final int WALL = 1; // 벽, 이동 불가능
	private static final int TIME = 3; // 시간의 벽
	private static final int SRC = 2; // 타임머신 있는 곳, 출발점
	private static final int DST = 4; // 탈출구 있는 곳, 도착점

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N; // 미지의 공간 크기 N * N
	private static int M; // 시간의 벽 크기 M * M * M 정육면체 중 밑면 제외
	private static int F; // 시간 이상 현상 개수

	private static int[][] unknownWorldMap; // 미지의 공간 N * N
	private static int[][] timeWallMap; // 시간의 벽
	private static int[][] errorInfo; // 시간 이상현상 정보

	private static int sy = -1;
	private static int sx = -1; // 타임머신 시작 위치
	private static int twy = -1;
	private static int twx = -1; // 시간의 벽에서 미지의 공간과 맞닿은 좌표
	private static int uwy = -1;
	private static int uwx = -1; // 시간의 벽을 탈출했을때 오게 되는 미지의 공간 좌표
	private static int uStartTime = -1; // 미지의 공간에 진입한 시간 (twy, twx) -> (uwy, uwx) 가는데도 시간 1 소요

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		F = Integer.parseInt(st.nextToken());

		unknownWorldMap = new int[N][N];
		timeWallMap = new int[3 * M][3 * M];
		errorInfo = new int[F + 1][4];

		for (int y = 0; y < 3 * M; y++)
			Arrays.fill(timeWallMap[y], WALL);

		for (int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 0; x < N; x++)
				unknownWorldMap[y][x] = Integer.parseInt(st.nextToken());
		}

		inputTimeWall();
		inputErrors();
		find();
		bfs1();

		System.out.println(bfs2());
	} // main-end

	private static void inputTimeWall() throws IOException {
		// 시간의 벽 입력
		// 동쪽
		for (int x = 0; x < M; x++) {
			st = new StringTokenizer(br.readLine());
			for (int y = M - 1; y >= 0; y--)
				timeWallMap[y + M][x + 2 * M] = Integer.parseInt(st.nextToken());
		}
		// 서쪽
		for (int x = M - 1; x >= 0; x--) {
			st = new StringTokenizer(br.readLine());
			for (int y = 0; y < M; y++)
				timeWallMap[y + M][x] = Integer.parseInt(st.nextToken());
		}
		// 남쪽
		for (int y = 0; y < M; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 0; x < M; x++)
				timeWallMap[y + 2 * M][x + M] = Integer.parseInt(st.nextToken());
		}
		// 북쪽
		for (int y = M - 1; y >= 0; y--) {
			st = new StringTokenizer(br.readLine());
			for (int x = M - 1; x >= 0; x--)
				timeWallMap[y][x + M] = Integer.parseInt(st.nextToken());
		}
		// 윗면
		for (int y = 0; y < M; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 0; x < M; x++) {
				timeWallMap[y + M][x + M] = Integer.parseInt(st.nextToken());
				if (timeWallMap[y + M][x + M] == SRC) {
					sy = y + M;
					sx = x + M;
				}
			}
		}
	}

	private static void inputErrors() throws IOException {
		for (int f = 1; f <= F; f++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken());
			int v = Integer.parseInt(st.nextToken());

			errorInfo[f][0] = y;
			errorInfo[f][1] = x;
			errorInfo[f][2] = d;
			errorInfo[f][3] = v;
		}
	}

	// 1. 미지의 공간을 확인하면서 시간의 벽과 맞닿은 곳(유일함)을 찾음
	private static void find() {
		int tsy = -1;
		int tsx = -1;
		int adjDir = -1; // 맞닿은 방향

		for (int y = 0; y < N; y++) {
			for (int x = 0; x < N; x++) {
				if (unknownWorldMap[y][x] == EMPTY) {
					for (int d = 0; d < 4; d++) {
						int ny = y + dy[d];
						int nx = x + dx[d];

						// 시간의 벽과 맞닿은 좌표를 찾았을 경우
						if (isIn(ny, nx, N - 1, N - 1) && unknownWorldMap[ny][nx] == TIME) {
							adjDir = d;
							uwy = y;
							uwx = x;
						}
					}
				} else if (unknownWorldMap[y][x] == TIME) {
					if (tsy == -1 && tsx == -1) {
						tsy = y;
						tsx = x;
					}
				}
			}
		}

		if (adjDir == 0) { // 동쪽으로 맞닿았을 경우, 서쪽벽에서 찾아야 함
			twy = M + uwy - tsy;
			twx = 0;
		} else if (adjDir == 1) { // 서쪽으로 맞닿았을 경우, 동쪽벽에서 찾아야 함
			twy = M + uwy - tsy;
			twx = 3 * M - 1;
		} else if (adjDir == 2) { // 남쪽으로 맞닿았을 경우, 북쪽벽에서 찾아야 함
			twy = 0;
			twx = M + uwx - tsx;
		} else { // 북쪽으로 맞닿았을 경우, 남쪽벽에서 찾아야 함
			twy = 3 * M - 1;
			twx = M + uwx - tsx;
		}
	}

	// (sy, sx)에서 (twy, twx)까지 가는 최단거리
	private static void bfs1() {
		Queue<int[]> queue = new LinkedList<>();
		int[][] dist = new int[3 * M][3 * M];

		for (int y = 0; y < 3 * M; y++)
			Arrays.fill(dist[y], INF);
		dist[sy][sx] = 0;
		queue.offer(new int[] { sy, sx, dist[sy][sx] });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];

			int ny = cy;
			int nx = cx;
			int nc = cc + 1;

			for (int d = 0; d < 4; d++) {
				ny = cy + dy[d];
				nx = cx + dx[d];

				if (isIn(ny, nx, 3 * M - 1, 3 * M - 1) && timeWallMap[ny][nx] != WALL && nc < dist[ny][nx]) {
					dist[ny][nx] = nc;
					queue.offer(new int[] { ny, nx, nc });
				}
			}

			// 옆면끼리 맞닿는 경우 체크
			if (0 <= cy && cy < M) { // 북쪽 벽일 경우
				ny = M;

				if (cx == M) // 서쪽 벽과 경계일 경우
					nx = cy;
				else if (cx == 2 * M - 1) // 동쪽 벽과 경계일 경우
					nx = 3 * M - 1 - cy;
			} else if (2 * M <= cy && cy < 3 * M) { // 남쪽 벽일 경우
				ny = 2 * M - 1;

				if (cx == M) // 서쪽 벽과 경계일 경우
					nx = 3 * M - 1 - cy;
				else if (cx == 2 * M - 1) // 동쪽 벽과 경계일 경우
					nx = cy;
			} else if (M <= cy && cy < 2 * M) { // 서쪽 또는 동쪽 벽일 경우
				if (0 <= cx && cx < M - 1) { // 서쪽 벽일 경우
					nx = M;

					if (cy == M) // 북쪽 벽과 경계일 경우
						ny = cx;
					else if (cy == 2 * M - 1) // 남쪽 벽과 경계일 경우
						ny = 3 * M - 1 - cx;
				} else if (2 * M <= cx && cx < 3 * M) { // 동쪽 벽일 경우
					nx = 2 * M - 1;

					if (cy == M) // 북쪽 벽과 경계일 경우
						ny = 3 * M - 1 - cx;
					else
						ny = cx;
				}
			}

			if (timeWallMap[ny][nx] != WALL && nc < dist[ny][nx]) {
				dist[ny][nx] = nc;
				queue.offer(new int[] { ny, nx, nc });
			}
		}

		uStartTime = dist[twy][twx] + 1;
	}

	private static int bfs2() {
		Queue<int[]> queue = new LinkedList<>();
		boolean[][] visited = new boolean[N][N];
		int[][] errorMap = new int[N][N]; // errorMap[y][x] : (y, x)에 시간 이상이 도달하는 시간
		setError(errorMap);

		visited[uwy][uwx] = true;
		queue.offer(new int[] { uwy, uwx, uStartTime });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int ctime = cur[2];

			for (int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];

				if (isIn(ny, nx, N - 1, N - 1) && (unknownWorldMap[ny][nx] == EMPTY || unknownWorldMap[ny][nx] == DST)
						&& !visited[ny][nx] && ctime + 1 < errorMap[ny][nx]) {
					if (unknownWorldMap[ny][nx] == DST)
						return ctime + 1;

					visited[ny][nx] = true;
					queue.offer(new int[] { ny, nx, ctime + 1 });
				}
			}
		}

		return -1;
	}

	private static void setError(int[][] errorMap) {
		Queue<int[]> queue = new LinkedList<>();
		for (int y = 0; y < N; y++)
			Arrays.fill(errorMap[y], INF);

		for (int f = 1; f <= F; f++) {
			int y = errorInfo[f][0];
			int x = errorInfo[f][1];

			errorMap[y][x] = 0;
			queue.offer(new int[] { y, x, f });
		}

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cf = cur[2];
			int d = errorInfo[cf][2];
			int v = errorInfo[cf][3];

			int ny = cy + dy[d];
			int nx = cx + dx[d];

			if (isIn(ny, nx, N - 1, N - 1) && unknownWorldMap[ny][nx] == EMPTY
					&& errorMap[cy][cx] + v < errorMap[ny][nx]) {
				errorMap[ny][nx] = errorMap[cy][cx] + v;
				queue.offer(new int[] { ny, nx, cf });
			}
		}
	}

	private static final int[] dy = { 0, 0, 1, -1 };
	private static final int[] dx = { 1, -1, 0, 0 };

	private static boolean isIn(int y, int x, int yBound, int xBound) {
		return (0 <= y && y <= yBound) && (0 <= x && x <= xBound);
	}
} // Main-class-end
```

### 부서별 평균 연봉 조회하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/284529)

```sql
select t1.dept_id, t1.dept_name_en, round(avg(t2.sal)) as avg_sal
from hr_department t1 inner join hr_employees t2
on t1.dept_id = t2.dept_id
group by t1.dept_id
order by avg_sal desc;
```

### **업그레이드 할 수 없는 아이템 구하기**

[](https://school.programmers.co.kr/learn/courses/30/lessons/273712)

```sql
select t3.item_id, t3.item_name, t3.rarity
from item_info t3 inner join
(select t2.item_id
from item_tree t1 right outer join item_tree t2
on t1.parent_item_id = t2.item_id
where t1.item_id is null) t4
on t3.item_id = t4.item_id
order by t3.item_id desc;
```