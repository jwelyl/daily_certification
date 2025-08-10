# 25_08_10_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 술래잡기**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/hide-and-seek/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	/**
	 * 술래 시야 내 도망자 잡을때 격자 범위 체크 안함 (런타임에러)
	 * 도망자 도망치려는 곳에 술래 있는지 체크할 때 좌표 비교 잘못함 (ny != sy || nx != sx) 로 비교해야 했음
	 */
	
	private static final int SIGHT = 3; // 술래 시야 고정 3

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N; // 격자 크기 N * N
	private static int M; // 도망자 수
	private static int H; // 나무 수
	private static int K; // 턴 수

	// seekerNumToPos[nth] : 술래가 달팽이 모양의 nth번째 위치에 있을때 {y좌표, x좌표}
	private static int[][] seekerNumToPos;

	// seekerPosToNum[y][x] : 술래가 N * N 맵의 (y, x)좌표에 있을때 달팽이 모양의 번호
	private static int[][] seekerPosToNum;

	// map[y][x] : (y, x)에 존재하는 도망자 번호 리스트
	private static List<Integer>[][] map;
	// treeMap[y][x] : (y, x)에 나무가 있으면 true
	private static boolean[][] treeMap;

	// runners[i] : i번째 도망자, 잡히면 null
	private static Runner[] runners;

	private static int sy;
	private static int sx; // 술래 N * N 맵에서의 좌표
	private static int snth; // 술래 달팽이 모양의 번호
	private static boolean souter; // souter = true면 중앙에서 바깥으로(snth 감소 방향) / souter = false면 바깝에서 중앙으로 (snth 증가 방향)

	private static int answer = 0; // K턴 동안 술래가 얻는 총 점수

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		H = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());

		sy = N / 2;
		sx = N / 2;
		snth = N * N; // 술래 처음에 중앙에 있음
		souter = true;

		seekerNumToPos = new int[N * N + 1][2];
		seekerPosToNum = new int[N][N];

		map = new ArrayList[N][N];
		for (int y = 0; y < N; y++) {
			for (int x = 0; x < N; x++)
				map[y][x] = new ArrayList<>();
		}
		treeMap = new boolean[N][N];

		runners = new Runner[M + 1];
		for (int num = 1; num <= M; num++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			int d = Integer.parseInt(st.nextToken());

			runners[num] = new Runner(y, x, d);
		}

		for (int h = 0; h < H; h++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			treeMap[y][x] = true;
		}

		makeSnail();

		for (int turn = 1; turn <= K; turn++) {
			run();
			move(turn);
		}

		System.out.println(answer);
	} // main-end

	// 도망자들이 움직임
	private static void run() {
		for (int y = 0; y < N; y++) {
			for (int x = 0; x < N; x++)
				map[y][x].clear();
		}

		for (int num = 1; num <= M; num++) {
			if (runners[num] != null) { // 아직 잡히지 않은 도망자면
				Runner runner = runners[num];
				int cy = runner.y;
				int cx = runner.x;
				int cdir = runner.dir;

				if (dist(cy, cx) <= 3) { // 술래와의 거리가 3 이하일 경우 도망침
					int ny = cy + dy[cdir];
					int nx = cx + dx[cdir];

					if (!isIn(ny, nx)) { // 움직이려는 칸이 격자 밖일 경우
						// 방향 반대로 변경
						cdir = cdir == 0 ? 2 : (cdir == 2 ? 0 : (cdir == 1 ? 3 : 1));
						ny = cy + dy[cdir];
						nx = cx + dx[cdir]; // 이동하려는 칸 재조정

						if (ny != sy || nx != sx) { // 이동하려는 칸에 술래가 없을 경우
							runner.y = ny;
							runner.x = nx;
							map[ny][nx].add(num); // 이동 성공
						} else // 이동하려는 칸에 술래가 있을 경우
							map[cy][cx].add(num); // 이동 실패

						runner.dir = cdir; // 이동 성공/실패와 관계 없이 도망자 방향 변경됨
					} else { // 움직이려는 칸이 격자 안일 경우
						if (ny != sy || nx != sx) { // 이동하려는 칸에 술래가 없을 경우
							runner.y = ny;
							runner.x = nx;
							map[ny][nx].add(num); // 이동 성공
						} else   // 이동하려는 칸에 술래가 있을 경우
							map[cy][cx].add(num); // 이동 실패
					}
				} 
			}
		}
	}

	private static void move(int turn) {
		int ny = sy;
		int nx = sx;
		int nnth = snth;

		if (souter) { // 바깥쪽으로 이동하는 경우
			nnth--;
			ny = seekerNumToPos[nnth][0];
			nx = seekerNumToPos[nnth][1]; // 술래가 이동하게 되는 칸

			if (nnth == 1) // 바깥쪽 이동이 끝난 경우
				souter = false; // 안쪽 이동으로 변경
		} else { // 안쪽으로 이동하는 경우
			nnth++;
			ny = seekerNumToPos[nnth][0];
			nx = seekerNumToPos[nnth][1]; // 술래가 이동하게 되는 칸

			if (nnth == N * N) // 안쪽 이동이 끝난 경우
				souter = true; // 바깥쪽 이동으로 변경
		}

		int dir = -1; // 현재 술래가 바라보는 방향
		int nnnth = souter ? nnth - 1 : nnth + 1;
		int nny = seekerNumToPos[nnnth][0];
		int nnx = seekerNumToPos[nnnth][1]; // 그 다음에 이동하게 될 칸

		int diffY = nny - ny;
		int diffX = nnx - nx;

		if (diffY == -1)
			dir = 2;
		else if (diffY == 1)
			dir = 0;
		else if (diffX == -1)
			dir = 3;
		else
			dir = 1;

		sy = ny;
		sx = nx;
		snth = nnth;

		int score = 0;

		for (int k = 0; k < SIGHT; k++) {
			int y = sy + dy[dir] * k;
			int x = sx + dx[dir] * k;

			if (!isIn(y, x)) // 바라보는 칸이 격자 벗어날 경우
				break;

			if (treeMap[y][x]) // 나무가 있을 경우 skip
				continue;

			for (int num : map[y][x]) {
				runners[num] = null; // (y, x)에 있던 num번째 술래 잡힘
				score++;
			}
		}

		score *= turn;
		answer += score;
	}

	private static void makeSnail() {
		int cy = 0;
		int cx = 0;
		int cnth = 0;
		int cdir = 0;

		while (cnth < N * N) {
			seekerPosToNum[cy][cx] = cnth + 1;
			seekerNumToPos[cnth + 1][0] = cy;
			seekerNumToPos[cnth + 1][1] = cx;

			int ny = cy + dy[cdir];
			int nx = cx + dx[cdir];

			if (!isIn(ny, nx) || seekerPosToNum[ny][nx] != 0) {
				cdir = (cdir + 1) % 4;
				ny = cy + dy[cdir];
				nx = cx + dx[cdir];
			}

			cy = ny;
			cx = nx;
			cnth++;
		}
	}

	// (ry, rx)와 (sy, sx)의 맨허튼 거리
	private static int dist(int ry, int rx) {
		return Math.abs(ry - sy) + Math.abs(rx - sx);
	}

	// 0 : 아래, 1 : 오른쪽, 2 : 위쪽, 3 : 왼쪽
	private static final int[] dy = { 1, 0, -1, 0 };
	private static final int[] dx = { 0, 1, 0, -1 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}

	private static class Runner {
		public int y;
		public int x;
		public int dir; // 움직이는 방향

		public Runner(int y, int x, int d) {
			this.y = y;
			this.x = x;

			// d = 1 : 좌우로 움직이는 사람, 항상 오른쪽을 보는 상태로 시작
			// d = 2 : 상하로 움직이는 사람, 항상 아래쪽을 보는 상태로 시작
			this.dir = d == 1 ? 1 : 0;
		}
	}
} // Main-class-end
```