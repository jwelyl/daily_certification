# 24_09_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5890 Tractor

[](https://www.acmicpc.net/problem/5890)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_002;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static final boolean[][] map = new boolean[MAX + 1][MAX + 1];
	private static final int[][] dist = new int[MAX + 1][MAX + 1];
	
	private static int sy;
	private static int sx;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		sy = Integer.parseInt(st.nextToken());
		sx = Integer.parseInt(st.nextToken());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			
			map[y][x] = true;
		}
		
		dijkstra();
		
		System.out.println(dist[0][0]);
	}	//	main-end
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y <= MAX) && (0 <= x && x <= MAX);
	}
	
	private static void dijkstra() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		
		for(int i = 0; i <= MAX; i++)
			Arrays.fill(dist[i], Integer.MAX_VALUE);
		
		dist[sy][sx] = 0;
		pq.offer(new Node(sy, sx, dist[sy][sx]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cy = cur.y;
			int cx = cur.x;
			int cc = cur.cost;
			
			if(dist[cy][cx] < cc)
				continue;
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nc = cc;
				
				if(isIn(ny, nx)) {
					nc += map[ny][nx] ? 1 : 0;
					
					if(nc < dist[ny][nx]) {
						dist[ny][nx] = nc;
						pq.offer(new Node(ny, nx, nc));
					}
				}
			}
		}
	}
	
	private static class Node implements Comparable<Node> {
		public int y;
		public int x;
		public int cost;
		
		public Node(int y, int x, int cost) {
			this.y = y;
			this.x = x;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Node other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```