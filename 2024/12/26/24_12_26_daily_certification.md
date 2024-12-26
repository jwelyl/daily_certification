# 24_12_26_daily_certification

```
[#361 koreii] 데일리인증 20241226
1. Spring DB
1-1. JPA
 - @Controller, @Service, @Repository
 - SQLExceptionTranslator
1-2. Spring Data JPA
 - Query Method
 - @Transactional
2. 코딩 테스트 대비 알고리즘 학습
- BFS, Dijkstra (BOJ 1400 화물차)
- BFS, Implementation, Simulation, Modulo Operation (BOJ 17822 원판 돌리기)
```

### **BOJ 1400 화물차**

[1400번: 화물차](https://boj.ma/1400/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int M;
	private static int N;
	private static int K;
	private static int sy;
	private static int sx;
	private static int ey;
	private static int ex;

	private static char[][] graph;
	private static boolean[][] visited;
	private static final List<Light> lights = new ArrayList<>();
	private static final List<State> states = new ArrayList<>();

	public static void main(String[] args) throws IOException {
		while (true) {
			lights.clear();
			states.clear();
			K = 0;

			st = new StringTokenizer(br.readLine());
			M = Integer.parseInt(st.nextToken());
			N = Integer.parseInt(st.nextToken());

			if (M == 0 && N == 0) {
				System.out.print(sb);
				break;
			}

			graph = new char[M][N];
			visited = new boolean[M][N];

			for (int y = 0; y < M; y++) {
				graph[y] = br.readLine().toCharArray();
				for (int x = 0; x < N; x++) {
					if ('0' <= graph[y][x] && graph[y][x] <= '9')
						K++;
					else if (graph[y][x] == 'A') {
						sy = y;
						sx = x;
					} else if (graph[y][x] == 'B') {
						ey = y;
						ex = x;
					}
				}
			}

			//	신호등 상태 입력
			for (int i = 0; i < K; i++) {
				st = new StringTokenizer(br.readLine());
				st.nextToken();
				char initStatus = st.nextToken().charAt(0);	//	신호등 초기 상태
				int ht = Integer.parseInt(st.nextToken());	//	동서 주기
				int vt = Integer.parseInt(st.nextToken());	//	남북 주기

				lights.add(new Light(ht, vt));
				if (initStatus == '-')
					states.add(new State('-', ht));
				else
					states.add(new State('|', vt));
			}

			br.readLine();
			sb.append(bfs()).append("\n");
		}
	}

	private static String bfs() {
		Queue<int[]> queue = new LinkedList<>();
		for (boolean[] row : visited)
			Arrays.fill(row, false);

		visited[sy][sx] = true;
		queue.offer(new int[] { sy, sx });

		int time = 0;

		while (!queue.isEmpty()) {
			int size = queue.size();

			for(int i = 0; i < size; i++) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];

				for (int d = 0; d < 4; d++) {
					int ny = cy + dy[d];
					int nx = cx + dx[d];

					if (ny == ey && nx == ex)	//	다음 칸이 도착칸일 경우
						return Integer.toString(time + 1);
					if (!isIn(ny, nx) || visited[ny][nx] || graph[ny][nx] == '.')
						continue;

					if (graph[ny][nx] == '#') {		// 다음 칸이 일반 도로일 경우
						visited[ny][nx] = true;
						queue.offer(new int[] { ny, nx });
					}
					else if ('0' <= graph[ny][nx] && graph[ny][nx] <= '9') {	//	다음 칸이 교차로일 경우
						int lightNum = graph[ny][nx] - '0';	//	신호등 번호
						char state = states.get(lightNum).state;	//	해당 신호등 현재 상태 

						if (state == '-') {
							if (d == 0 || d == 2) { // 동서
								visited[ny][nx] = true;
								queue.offer(new int[] { ny, nx });
							} 
							else // 대기
								queue.offer(new int[] { cy, cx });
						} else if (state == '|') {
							if (d == 1 || d == 3) { // 남북
								visited[ny][nx] = true;
								queue.offer(new int[] { ny, nx });
							} 
							else // 대기
								queue.offer(new int[] { cy, cx });
						}
					}
				}
			}

			time++;
			updateLights();
		}

		return "impossible";
	}

	//	신호등 상태를 변경함
	private static void updateLights() {
		for (int i = 0; i < states.size(); i++) {
			State state = states.get(i);	//	i번째 신호등의 현재 상태
			state.time--;
			
			if (state.time == 0) {	//	상태 바뀔 때가 됐을 경우
				if (state.state == '-') {	//	현재 상태가 동서 상태일 경우
					state.state = '|';		//	남북으로 바꿈
					state.time = lights.get(i).vt;	//	남은 시간을 남북 주기로 변경
				} else {	//	현재 상태가 남북 상태일 경우
					state.state = '-';		//	동서로 바꿈
					state.time = lights.get(i).ht;	//	남은 시간을 동서 주기로 변경
				}
			}
		}
	}

	private static final int[] dy = { 0, 1, 0, -1 };
	private static final int[] dx = { 1, 0, -1, 0 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < M) && (0 <= x && x < N);
	}
	
	private static class Light {	//	신호등
		int ht;	//	동서 방향 주기
		int vt;	//	남북 방향 주기

		public Light(int horizontalTime, int verticalTime) {
			this.ht = horizontalTime;
			this.vt = verticalTime;
		}
	}

	private static class State {	//	신호등 상태
		public char state;	//	'-' : 동서, '|' : 남북
		public int time;	//	상태 남은 시간

		public State(char state, int time) {
			this.state = state;
			this.time = time;
		}
	}
} // Main-class-end
```

### **BOJ 17822 원판 돌리기**

[17822번: 원판 돌리기](https://boj.ma/17822/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int T;
	
	private static int[][] board;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		board = new int[N][M];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++)
				board[y][x] = Integer.parseInt(st.nextToken());
		}
		
		for(int t = 0; t < T; t++) {
			st = new StringTokenizer(br.readLine());
			int x = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken());
			int k = Integer.parseInt(st.nextToken());
			
			rotate(x, d, k);	//	회전 시키기
			bfs();	//	같은 수 지우기, 같은 수 없을 경우 평균 구해서 평균보다 작은 수는 +1, 큰 수는 -1하기 
		}
		
		System.out.println(answer());
	}	//	main-end
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	private static void bfs(int y, int x, int num) {
		Queue<int[]> queue = new LinkedList<>();
		board[y][x] = NONE;	//	(y, x)칸을 지움
		queue.offer(new int[] {y, x});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = (cx + dx[d] + M) % M;
				
				if(isIn(ny, nx) && board[ny][nx] == num) {
					board[ny][nx] = NONE;
					queue.offer(new int[] {ny, nx});
				}
			}
		}
	}
	
	private static void bfs() {
		double avg = 0;	//	원판에 남아있는 수의 평균
		int cnt = 0;	//	원판에 남아있는 수의 개수
		
		boolean isSame = false;	//	같은 수가 한번이라도 있을 경우, true

		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++) {
				int num = board[y][x];
				
				if(num == NONE)	//	해당 칸에는 수가 없을 경우
					continue;
				
				avg += num;
				cnt++;
				
				for(int d = 0; d < 4; d++) {
					int ny = y + dy[d];
					int nx = (x + dx[d] + M) % M;
					
					if(isIn(ny, nx)) {
						if(board[ny][nx] == num) {	//	주변에 같은 수가 있을 경우
							isSame = true;
							bfs(y, x, num);
						}
					}
				}
			}
		}
		
		if(!isSame) {	//	같은 수가 한번도 없었을 경우
			avg /= cnt;
			
			for(int y = 0; y < N; y++) {
				for(int x = 0; x < M; x++) {
					if(board[y][x] == NONE)
						continue;
					
					if((double)board[y][x] < avg)	//	평균보다 작은 수일 경우
						board[y][x]++;	//	1을 더함
					else if((double)board[y][x] > avg)	//	평균보다 큰 수일 경우
						board[y][x]--;	//	1을 뺌
				}
			}
		}
	}
	
	private static int answer() {
		int res = 0;
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++)
				res += board[y][x] == NONE ? 0 : board[y][x];
		}
		
		return res;
	}
	
	private static void rotate(int x, int d, int k) {
		for(int i = x; i - 1 < N; i += x)
			rotate(i - 1, d == 0 ? k : -k);
	}
	
	//	y번째 행을 k칸만큼 회전, k > 0일 경우 시계방향, k < 0일 경우 반시계방향
	private static void rotate(int y, int k) {
		int[] tmp = new int[M];
		
		for(int x = 0; x < M; x++)
			tmp[(x + k + M) % M] = board[y][x];
		
		board[y] = tmp;
	}
}	//	Main-class-end
```