# 25_09_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5213 과외맨

[16137번: 견우와 직녀](http://boj.ma/16137/t)

**0-1 BFS**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0; // 갈 수 없는 칸
	private static final int INF = Integer.MAX_VALUE;

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int H;
	private static int W; // 필요한 맵 크기 H * W
	private static int C; // 전체 블록 수

	// numMap[y][x] : (y, x)칸에 쓰인 숫자 (1 .. 6)
	private static int[][] numMap;
	// blockNumMap[y][x] : (y, x)칸의 블록 번호 (1 .. N^2 - N / 2)
	private static int[][] blockNumMap;

	private static int line = 0; // 블록이 놓일 라인
	private static int cnt = 0; // 해당 라인에 블록이 놓인 개수 (line이 짝수일 경우 N개, line이 홀수일 경우 N - 1개)
	private static int pos = 0; // 해당 라인에 놓일 블록의 위치 (line이 짝수일 경우 x = 0부터, 홀수일 경우 x = 1부터 시작, 2씩 증가)

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		H = N;
		W = 2 * N;
		C = N * N - N / 2;

		numMap = new int[H][W];
		blockNumMap = new int[H][W];

		for (int num = 1; num <= C; num++) {
			st = new StringTokenizer(br.readLine());
			int num1 = Integer.parseInt(st.nextToken());
			int num2 = Integer.parseInt(st.nextToken());

			numMap[line][pos] = num1;
			numMap[line][pos + 1] = num2;
			blockNumMap[line][pos] = num;
			blockNumMap[line][pos + 1] = num;
			cnt++;
			pos += 2;

			if (line % 2 == 0) { // 짝수번째 라인일 경우
				if (cnt == N) { // 짝수번째 라인에 블록을 모두 놓았을 경우
					line++; // 이제 홀수번째 라인에 블록을 놓아야 함
					cnt = 0;
					pos = 1; // 홀수번째 라인에서는 x = 1부터 놓음
				}
			} else { // 홀수번째 라인일 경우
				if (cnt == N - 1) { // 홀수번째 라인에 블록을 모두 놓았을 경우
					line++; // 이제 짝수번째 라인에 블록을 놓아야 함
					cnt = 0;
					pos = 0; // 짝수번째 라인에서는 x = 0부터 놓음
				}
			}
		}

		bfs();
	} // main-end

	private static void bfs() {
		Deque<int[]> deque = new ArrayDeque<>();
		int[][] dist = new int[H][W];
		int[] prevs = new int[H * W];
		
		Arrays.fill(prevs, -1);
		for(int y = 0; y < H; y++)
			Arrays.fill(dist[y], INF);
		
		dist[0][0] = 0;
		deque.offerFirst(new int[] {0, 0, 0});
		
		while(!deque.isEmpty()) {
			int[] cur = deque.pollFirst();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];
			int cv = cy * W + cx;
			
			int cnum = numMap[cy][cx];
			int cBlockNum = blockNumMap[cy][cx];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && blockNumMap[ny][nx] != NONE) {
					int nc = cc;
					int nv = ny * W + nx;
					
					int nnum = numMap[ny][nx];
					int nBlockNum = blockNumMap[ny][nx];
			
					if(cBlockNum == nBlockNum)	{	//	같은 블록일 경우
						if(nc < dist[ny][nx]) {
							dist[ny][nx] = nc;
							deque.offerFirst(new int[] {ny, nx, nc});
							prevs[nv] = cv;
						}
					}
					else if(cnum == nnum) {	//	다른 블록이지만 이동 가능할 경우
						nc++;
						
						if(nc < dist[ny][nx]) {
							dist[ny][nx] = nc;
							deque.offerLast(new int[] {ny, nx, nc});
							prevs[nv] = cv;
						}
					}
				}
			}
		}
		
		// 도달 가능한 '가장 큰 블록 번호'의 셀 찾기
		int ey = -1;
		int ex = -1;
		int ev = -1;
		
		OUTER: 
		for (int y = H - 1; y >= 0; y--) {
			for (int x = W - 1; x >= 0; x--) {
				if (blockNumMap[y][x] == NONE)
					continue;
				
				if(dist[y][x] != INF) {	//	도달한 경우
					ey = y;
					ex = x;
					ev = y * W + x;
					
					break OUTER;
				}
			}
		}
		
		// 블록 중복 방지
		boolean[] used = new boolean[C + 1];
		Deque<Integer> path = new ArrayDeque<>();
		while (ev != -1) {
			int blockNum = blockNumMap[ey][ex];
			if (!used[blockNum]) {
				used[blockNum] = true;
				path.offerLast(blockNum);
			}
			
			ev = prevs[ev];
			ey = ev / W;
			ex = ev % W;
		}

		sb.append(path.size()).append('\n');
		while (!path.isEmpty())
			sb.append(path.pollLast()).append(' ');
		System.out.println(sb);
	}

	private static final int[] dy = { 0, 1, 0, -1 };
	private static final int[] dx = { 1, 0, -1, 0 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < H) && (0 <= x && x < W);
	}
} // Main-class-end
```

**BFS (같은 블록에 해당하는 타일 2개씩 큐에 넣기)**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Deque;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0; // 갈 수 없는 칸

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int H;
	private static int W; // 필요한 맵 크기 H * W
	private static int C; // 전체 블록 수

	// numMap[y][x] : (y, x)칸에 쓰인 숫자 (1 .. 6)
	private static int[][] numMap;
	// blockNumMap[y][x] : (y, x)칸의 블록 번호 (1 .. N^2 - N / 2)
	private static int[][] blockNumMap;

	private static int line = 0; // 블록이 놓일 라인
	private static int cnt = 0; // 해당 라인에 블록이 놓인 개수 (line이 짝수일 경우 N개, line이 홀수일 경우 N - 1개)
	private static int pos = 0; // 해당 라인에 놓일 블록의 위치 (line이 짝수일 경우 x = 0부터, 홀수일 경우 x = 1부터 시작, 2씩 증가)

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		H = N;
		W = 2 * N;
		C = N * N - N / 2;

		numMap = new int[H][W];
		blockNumMap = new int[H][W];

		for (int num = 1; num <= C; num++) {
			st = new StringTokenizer(br.readLine());
			int num1 = Integer.parseInt(st.nextToken());
			int num2 = Integer.parseInt(st.nextToken());

			numMap[line][pos] = num1;
			numMap[line][pos + 1] = num2;
			blockNumMap[line][pos] = num;
			blockNumMap[line][pos + 1] = num;
			cnt++;
			pos += 2;

			if (line % 2 == 0) { // 짝수번째 라인일 경우
				if (cnt == N) { // 짝수번째 라인에 블록을 모두 놓았을 경우
					line++; // 이제 홀수번째 라인에 블록을 놓아야 함
					cnt = 0;
					pos = 1; // 홀수번째 라인에서는 x = 1부터 놓음
				}
			} else { // 홀수번째 라인일 경우
				if (cnt == N - 1) { // 홀수번째 라인에 블록을 모두 놓았을 경우
					line++; // 이제 짝수번째 라인에 블록을 놓아야 함
					cnt = 0;
					pos = 0; // 짝수번째 라인에서는 x = 0부터 놓음
				}
			}
		}

		bfs();
	} // main-end

	private static void bfs() {
		Queue<Integer> queue = new LinkedList<>();
		boolean[] visited = new boolean[C + 1];		//	해당 블록에 방문한 적 있는지 체크
		int[] prevs = new int[C + 1];				//	prevs[block] : block 이전에 방문한 블록
		
		Arrays.fill(prevs, -1);
		
		visited[1] = true;
		queue.offer(0 * W + 0);
		queue.offer(0 * W + 1);	//	1번 블록을 이루는 두개의 타일 (0, 0), (0, 1)을 함께 큐에 넣기
		
		while(!queue.isEmpty()) {
			int cur = queue.poll();
			int cy = cur / W;
			int cx = cur % W;
			
			int cnum = numMap[cy][cx];				//	(cy, cx)에 존재하는 타일 번호
			int cBlockNum = blockNumMap[cy][cx];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	인접한 칸
				
				if(isIn(ny, nx)) {
					int nnum = numMap[ny][nx];
					int nBlockNum = blockNumMap[ny][nx];
					
					if(nBlockNum == NONE || visited[nBlockNum] || nnum != cnum)
						continue;
					
					int nnx = nx - 1;	//	(ny, nx)와 같은 블록에 해당하는 타일 찾기 (왼쪽부터 확인)
					
					if(!isIn(ny, nnx) || blockNumMap[ny][nnx] != nBlockNum)	//	왼쪽 타일이 같은 타일이 아닐 경우
						nnx = nx + 1;	//	오른쪽 타일
				
					visited[nBlockNum] = true;
					queue.offer(ny * W + nx);
					queue.offer(ny * W + nnx);
					prevs[nBlockNum] = cBlockNum;
				}
			}
		}
		
		for(int blockNum = C; blockNum >= 1; blockNum--) {
			if(visited[blockNum]) {
				Deque<Integer> deque = new LinkedList<>();
				
				while(blockNum != -1) {
					deque.offerLast(blockNum);
					blockNum = prevs[blockNum];
				}
				
				sb.append(deque.size()).append("\n");
				while(!deque.isEmpty())
					sb.append(deque.pollLast()).append(" ");
				System.out.println(sb);
				
				break;
			}
		}
	}

	private static final int[] dy = { 0, 1, 0, -1 };
	private static final int[] dx = { 1, 0, -1, 0 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < H) && (0 <= x && x < W);
	}
} // Main-class-end
```