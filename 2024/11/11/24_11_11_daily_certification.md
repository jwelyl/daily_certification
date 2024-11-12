# 24_11_11_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 2423 전구를 켜라**

[](https://www.acmicpc.net/problem/2423)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static char[][] map;
	private static int[][] dist;

	private static int res = 0;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());

		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		map = new char[N][M];
		dist = new int[N][M];

		for (int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			Arrays.fill(dist[y], INF);
		}

		if (map[0][0] != '\\') {
			res++;
			map[0][0] = '\\';
		}
		if (map[N - 1][M - 1] != '\\') {
			res++;
			map[N - 1][M - 1] = '\\';
		}

		dijkstra();
		
		System.out.println(dist[N - 1][M - 1] == INF ? "NO SOLUTION" : (dist[N - 1][M - 1] + res));
	}

	private static void dijkstra() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		pq.offer(new Node(0, 0, '\\', 0));
		dist[0][0] = 0;

		while (!pq.isEmpty()) {
			Node cur = pq.poll();

			if (cur.y == N - 1 && cur.x == M - 1) {
				dist[N - 1][M - 1] = cur.cost;
				return;
			}

			for (int d = 0; d < 4; d++) {
				if(cur.circuit == '\\' && d % 2 == 0)
					continue;
				if(cur.circuit == '/' && d % 2 == 1)
					continue;

				int ny = cur.y + dy1[d];
				int nx = cur.x + dx1[d];

				if (!isIn(ny, nx))
					continue;

				if (cur.circuit == map[ny][nx] && cur.cost < dist[ny][nx]) {	//	대각선일 경우 회로 방향이 같아야 함
					dist[ny][nx] = cur.cost;
					pq.offer(new Node(ny, nx, cur.circuit, cur.cost));
				} else if (cur.circuit != map[ny][nx] && cur.cost + 1 < dist[ny][nx] && (ny != N - 1 || nx != M - 1)) {	//	회로 방향이 반대일 경우 회로 회전, 전구와 맞닿은 칸의 회로는 회전 불가
					dist[ny][nx] = cur.cost + 1;
					pq.offer(new Node(ny, nx, cur.circuit, cur.cost + 1));
				}
			}

			for (int d = 0; d < 4; d++) {
				int ny = cur.y + dy2[d];
				int nx = cur.x + dx2[d];

				if (!isIn(ny, nx))
					continue;

				if (cur.circuit != map[ny][nx] && cur.cost < dist[ny][nx]) { // 수직 수평 방향의 경우 방향이 회로 방향이 반대여야 함
					dist[ny][nx] = cur.cost;
					pq.offer(new Node(ny, nx, map[ny][nx], cur.cost));
				} else if (cur.circuit == map[ny][nx] && cur.cost + 1 < dist[ny][nx] && (ny != N - 1 || nx != M - 1)) { // 회로 방향이 같을 경우 회로 회전, 전구와 맞닿은 칸의 회로는 회전 불가
					dist[ny][nx] = cur.cost + 1;
					pq.offer(new Node(ny, nx, cur.circuit == '/' ? '\\' : '/', cur.cost + 1));
				}
			}
		}

	}
	
	private static final int[] dy1 = {-1, 1, 1, -1};
	private static final int[] dx1 = {1, 1, -1, -1};
	
	private static final int[] dy2 = {0, 1, 0, -1};
	private static final int[] dx2 = {1, 0, -1, 0};

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}

	private static class Node implements Comparable<Node> {
		public int y; 
		public int x; 
		public char circuit;
		public int cost;

		public Node(int y, int x, char circuit, int cost) {
			this.y = y;
			this.x = x;
			this.circuit = circuit;
			this.cost = cost;
		}
		
		public int compareTo(Node other) {
			return Integer.compare(cost, other.cost);
		}
	}
}	//	Main-class-end
```