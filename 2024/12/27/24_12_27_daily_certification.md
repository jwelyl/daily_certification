# 24_12_27_daily_certification

```
[#362 koreii] 데일리인증 20241227
1. Spring DB
1-1. QueryDSL
1-2. Service, Repository
- DI, OCP
- Abstraction
2. 코딩 테스트 대비 알고리즘 학습
- Dijkstra (Multisource) (BOJ 8757 Bubu)
```

### **BOJ 8757 Bubu**

[8757번: Bubu](https://boj.ma/8757/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static final long INF = Long.MAX_VALUE;
	
	private static int N;
	private static int M;
	private static int S;
	private static int X;
	
	private static List<int[]>[] graph;
	private static final PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
	
	private static long[][] dist;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
	    M = Integer.parseInt(st.nextToken());
		S = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		dist = new long[2][N + 1];
		Arrays.fill(dist[0], INF);
		Arrays.fill(dist[1], INF);
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
		    int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, c});
			graph[v2].add(new int[] {v1, c});
		}
		
		for(int s = 0; s < S; s++) {
			int v = Integer.parseInt(br.readLine());
			dist[0][v] = 0;
			pq.offer(new long[] {v, dist[0][v]});
		}
		
		dijkstra(false);
		
		X = Integer.parseInt(br.readLine());
		dist[1][X] = 0;
		pq.clear();
		pq.offer(new long[] {X, dist[1][X]});
		
		dijkstra(true);
		
		System.out.println(dist[1][1] == INF ? -1 : dist[1][1]);
	}	//	main-end
	
	
	private static void dijkstra(boolean bubu) {
		long[] distArr = bubu ? dist[1] : dist[0];
		
		while(!pq.isEmpty()) {
			long[] cur = pq.poll();
			int cv = (int)cur[0];
			long cc = cur[1];
			
			if(distArr[cv] < cc)
				continue;
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];
				long nc = cc + edge[1];
				
				if(bubu && dist[0][nv] <= nc)
					continue;
				
				if(nc < distArr[nv]) {
					distArr[nv] = nc;
					pq.offer(new long[] {nv, distArr[nv]});
				}
			}
		}
	}
}	//	Main-class-end
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