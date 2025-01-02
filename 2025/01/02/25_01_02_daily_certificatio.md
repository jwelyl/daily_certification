# 25_01_02_daily_certification

```
[#002 koreii] 데일리인증 20250102
1. 2025년 목표 설정
- 1월 주요 과제 선정
2. 코딩 테스트 대비 알고리즘 학습
- Implementation, Simulation, Bruteforcing (BOJ 15683 감시)
- Implementation, Simulation, BFS (BOJ 16234 인구 이동)
```

### **BOJ** 15683 감시

[15683번: 감시](https://boj.ma/15683/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int WALL = 6;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int[][] map;
	private static final List<int[]> cctvs = new ArrayList<>();
	private static int[] status;	//	CCTV 회전 상태
	
	private static int empty = 0;	//	빈칸 (CCTY, 벽 없는 칸) 개수
	private static int answer = 0;	//	사각지대 최소 개수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new int[N][M];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++) {
				map[y][x] = Integer.parseInt(st.nextToken());
				
				if(map[y][x] == 0)
					empty++;
				else if(map[y][x] != WALL)	//	CCTV일 경우
					cctvs.add(new int[] {y, x});
			}
		}
		
		answer = empty;
		status = new int[cctvs.size()];
		
		perm(0);
		
		System.out.println(answer);
	}	//	main-end
	
	private static void perm(int nth) {
		if(nth == status.length) {
			boolean[][] visited = new boolean[N][M];
			int cnt = empty;	//	사각지대 개수
			
			for(int i = 0; i < cctvs.size(); i++) {	//	각각의 cctv에 대하여
				int[] cctv = cctvs.get(i);			//	i번째 cctv
				int y = cctv[0];
				int x = cctv[1];					//	i번째 cctv 위치
				int type = map[y][x];				//	i번째 cctv 타입
				int[] dirs = TYPE_DIRS[type];		//	i번째 cctv가 감시할 수 있는 방향 배열
				int rot = status[i];				//	i번째 cctv 회전 상태
				
				for(int d : dirs) {	//	감시할 수 있는 방향 하나에 대하여
					d = (d + rot) % 4;	//	회전 상태를 고려하여 보정된 방향
					
					for(int k = 1; ; k++) {
						int ny = y + dy[d] * k;
						int nx = x + dx[d] * k;
						
						if(!isIn(ny, nx) || map[ny][nx] == WALL)	//	범위를 벗어나거나, 벽에 가로막힐 경우
							break;
						if(map[ny][nx] == 0 && !visited[ny][nx]) {	//	빈칸이고 아직 cctv에 의해 커버되지 않은 경우
							visited[ny][nx] = true;
							cnt--;	//	사각지대 개수 1개 감소
						}
					}
				}
			}
			
			answer = Math.min(answer, cnt);
			
			return;
		}
		
		for(int rot = 0; rot < 4; rot++) {
			status[nth] = rot;
			perm(nth + 1);
		}
	}
	
	private static final int[][] TYPE_DIRS = {
			{},
			{0},				//	1번 CCTV 감시 가능 방향
			{0, 2},				//	2번
			{0, 3},				//	3번
			{0, 2, 3},			//	4번
			{0, 1, 2, 3}		//	5번
	};

	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```

### **BOJ** 16234 인구 이동

[16234번: 인구 이동](https://boj.ma/16234/t)

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
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int L;
	private static int R;
	private static int[][] map;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		L = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		map = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				map[y][x] = Integer.parseInt(st.nextToken());
		}
		
		while(true) {
			boolean moved = false;	//	인구 이동 없으면 false
			
			boolean[][] visited = new boolean[N][N];
			
			for(int y = 0; y < N; y++) {
				for(int x = 0; x < N; x++) {
					if(visited[y][x])
						continue;
					
					if(bfs(y, x, visited) >= 2)	//	서로 국경 열린 나라가 2개 이상인 경우, 인구 이동 발생
						moved = true;
				}
			}
			
			if(!moved) {	//	더 이상 인구 이동이 없음
				System.out.println(answer);
				break;
			}
			
			answer++;	//	인구 이동 발생함
		}
	}	//	main-end
	
	private static int bfs(int y, int x, boolean[][] visited) {
		Queue<int[]> queue = new LinkedList<>();
		List<int[]> posList = new ArrayList<>();	//	국경 열린 나라들의 좌표
		int res = 1;	//	(y, x)는 무조건 포함
		int sum = map[y][x];

		int[] init = {y, x};
		visited[y][x] = true;
		queue.offer(init);
		posList.add(init);
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx)) {
					int diff = Math.abs(map[ny][nx] - map[cy][cx]);
					
					if(L <= diff && diff <= R && !visited[ny][nx]) {
						int[] next = {ny, nx};
						visited[ny][nx] = true;
						queue.offer(next);
						posList.add(next);
						sum += map[ny][nx];
						res++;
					}
				}
			}
		}
		
		for(int[] pos : posList)
			map[pos[0]][pos[1]] = sum / res;
		
		return res;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```