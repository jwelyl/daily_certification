# 24_09_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13349 **Millionaire Madness**

[](https://www.acmicpc.net/problem/13349)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 1_000_000_000;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;

	private static int[][] heights;
	private static int[][] dist;
	
	private static int max = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		heights = new int[N][M];
		dist = new int[N][M];
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			for(int j = 0; j < M; j++) {
				heights[i][j] = Integer.parseInt(st.nextToken());
				max = Math.max(max, heights[i][j]);
			}
		}
		
		System.out.println(parametricSearch());
	}	//	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = max;
		int ret = max;
		
		while(start <= end) {
			int mid = (start + end) / 2;	//	최대 길이 mid의 사다리
			
			if(dijkstra(mid)) {	//	최대 길이 mid의 사다리로 (0, 0)에서 (N - 1, N - 1)까지 갈 수 있을 경우
				ret = mid;	//	일단 mid 저장
				end = mid - 1;	//	mid 더 줄여보기
			}
			else start = mid + 1;
		}
		
		return ret;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	//	최대 길이 diff의 사다리로 (0, 0)에서 (N - 1, M - 1)로 갈 수 있으면 true
	private static boolean dijkstra(int diff) {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		
		for(int i = 0; i < N; i++)
			Arrays.fill(dist[i], INF);
		
		dist[0][0] = 0;
		pq.offer(new Node(0, 0, 0));
		
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
				int nc = cc + 1;
				
				//	다음에 가려는 칸이 범위 밖이거나, diff 길이의 사다리로 올라갈 수 없을 정도로 높을 경우
				if(!isIn(ny, nx) || heights[ny][nx] > heights[cy][cx] + diff)
					continue;
				
				if(nc < dist[ny][nx]) {
					dist[ny][nx] = nc;
					pq.offer(new Node(ny, nx, dist[ny][nx]));
				}
			}
		}
		
		return dist[N - 1][M - 1] != INF;
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