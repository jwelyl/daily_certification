# 25_01_18_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 14611** 월요병

[14611번: 월요병](https://boj.ma/14611/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final long INF = 1_000_000_000L * 300 * 300 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	행 수
	private static int M;	//	열 수
	
	private static long[][] map;
	
	private static long answer = INF;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new long[N][M];

		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++) {
				map[y][x] = Integer.parseInt(st.nextToken());	//	(y, x)에 벽을 세우는 비용
				
				if(map[y][x] == -1)	//	벽을 세울 수 없을 경우
					map[y][x] = INF;
				else if(map[y][x] == -2)	//	이미 벽이 있는 경우
					map[y][x] = 0;
			}
		}
		
		dijkstra(1);
		dijkstra(2);
		
		System.out.println(answer == INF ? -1 : answer);
	}	//	main-end
	
	private static void dijkstra(int type) {
		PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[2], n2[2]));
		long[][] dist = new long[N][M];
		for(int y = 0; y < N; y++)
			Arrays.fill(dist[y], INF);
		
		//	type = 1일 경우 y = 0에서 출발해서 x = 0 또는 y = N - 1로 도달해야 함
		if(type == 1) {
			for(int x = 0; x < M; x++) {
				if(map[0][x] != INF) {
					dist[0][x] = map[0][x];
					pq.offer(new long[] {0, x, dist[0][x]});
				}
			}
		}
		//	type = 2일 경우 x = M - 1에서 출발해서 x = 0 또는 y = N - 1로 도달해야 함
		else {
			for(int y = 0; y < N; y++) {
				if(map[y][M - 1] != INF) {
					dist[y][M - 1] = map[y][M - 1];
					pq.offer(new long[] {y, M - 1, dist[y][M - 1]});
				}
			}
		}
		
		while(!pq.isEmpty()) {
			long[] cur = pq.poll();
			int cy = (int)cur[0];
			int cx = (int)cur[1];
			long cc = cur[2];
			
			if(dist[cy][cx] < cc)
				continue;
			
			for(int d = 0; d < 8; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && cc + map[ny][nx] < dist[ny][nx]) {
					dist[ny][nx] = cc + map[ny][nx];
					pq.offer(new long[] {ny, nx, dist[ny][nx]});
				}
			}
		}
		
		for(int y = 0; y < N; y++)
			answer = Math.min(answer, dist[y][0]);
		for(int x = 0; x < M; x++)
			answer = Math.min(answer, dist[N - 1][x]);
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```