# 24_09_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2645 회로배치

[](https://www.acmicpc.net/problem/2645)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;	//	격자판 크기
	private static int K;	//	회로 위를 지나갈 때 비용
	private static int M;	//	이미 존재하는 회로 개수
	
	private static int sy;
	private static int sx;	//	시작 정점
	private static int ey;
	private static int ex;	//	끝 정점
	
	private static boolean[][] map;	//	격자에 회로가 놓여져 있으면 true, 아니면 false
	private static int[][] dist;	//	dist[y][x] : 끝점 (ey, ex)에서 (y, x)로 이동하는 데 드는 비용
	
	private static Pos[][] prev;	//	prev[y][x] : (ey, ex)에서 (sy, sx)로 가는 최단 경로에서 (y, x)의 이전 정점
	
	private static final List<Pos> path = new ArrayList<>();	//	(sy, sx)에서 (ey, ex)까지의 경로
	private static final List<Pos> edgePoints = new ArrayList<>();	//	경로 시작점, 꺾이는 점, 끝점

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		map = new boolean[N + 1][N + 1];
		dist = new int[N + 1][N + 1];
		prev = new Pos[N + 1][N + 1];
		
		for(int y = 0; y <= N; y++) {
			for(int x = 0; x <= N; x++) {
				dist[y][x] = INF;
				prev[y][x] = new Pos();
			}
		}
		
		st = new StringTokenizer(br.readLine());
		sy = Integer.parseInt(st.nextToken());
		sx = Integer.parseInt(st.nextToken());
		ey = Integer.parseInt(st.nextToken());
		ex = Integer.parseInt(st.nextToken());
		
		K = Integer.parseInt(br.readLine());
		M = Integer.parseInt(br.readLine());
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			int cnt = Integer.parseInt(st.nextToken());
			
			int fy = Integer.parseInt(st.nextToken());
			int fx = Integer.parseInt(st.nextToken());	//	회로 한 선분 시작점
			
			for(int i = 0; i < cnt - 1; i++) {
				int ty = Integer.parseInt(st.nextToken());
				int tx = Integer.parseInt(st.nextToken());	//	회로 한 선분 끝점
				
				int dir = getDir(fy, fx, ty, tx);
				
				int ny = fy;
				int nx = fx;
				
				while(ny != ty || nx != tx) {
					map[ny][nx] = true;
					ny += dy[dir];
					nx += dx[dir];
				}
				
				map[ty][tx] = true;
				fy = ty;
				fx = tx;
			}
		}
		
		dijkstra();
		
		int cy = sy;
		int cx = sx;
		int ny = 0;
		int nx = 0;
		
		while(cy != 0 && cx != 0) {
			path.add(new Pos(cy, cx));
			
			ny = prev[cy][cx].y;
			nx = prev[cy][cx].x;
			
			cy = ny;
			cx = nx;
		}
		
		Pos cur = path.get(0);
		Pos next = path.get(1);
		cy = cur.y;
		cx = cur.x;
		edgePoints.add(new Pos(cy, cx));	//	시작점 넣기
		ny = next.y;
		nx = next.x;
		
		int curDir = getDir(cy, cx, ny, nx);
		
		for(int i = 1; i < path.size() - 1; i++) {
			cur = path.get(i);
			cy = cur.y;
			cx = cur.x;
			next = path.get(i + 1);
			ny = next.y;
			nx = next.x;
			
			int nextDir = getDir(cy, cx, ny, nx);
			
			if(nextDir != curDir)	//	(cy, cx)에서 방향이 바뀔 경우
				edgePoints.add(new Pos(cy, cx));
			
			curDir = nextDir;
		}
		
		edgePoints.add(path.get(path.size() - 1));	//	마지막 점 추가
		
		sb.append(dist[sy][sx]).append("\n").append(edgePoints.size()).append(" ");
		for(Pos pos : edgePoints)
			sb.append(pos.y).append(" ").append(pos.x).append(" ");

		System.out.println(sb);
	}	//	main-end
	
	private static void dijkstra() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		dist[ey][ex] = map[ey][ex] ? K : 1;
		pq.offer(new Node(ey, ex, dist[ey][ex]));
		
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
				
				if(isIn(ny, nx) ) {
					nc += map[ny][nx] ? K : 1;
					
					if(nc < dist[ny][nx]) {
						dist[ny][nx] = nc;
						prev[ny][nx] = new Pos(cy, cx);
						
						pq.offer(new Node(ny, nx, dist[ny][nx]));
					}
				}
			}
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= N) && (1 <= x && x <= N);
	}
	
	private static int getDir(int y1, int x1, int y2, int x2) {
		if(y1 == y2) {	//	수평 방향으로 이동할 경우
			if(x2 - x1 > 0)
				return 0;
			else
				return 2;
		}
		else {	//	수직 방향으로 이동할 경우
			if(y2 - y1 > 0)
				return 1;
			else
				return 3;
		}
	}
	
	private static class Pos {
		public int y;
		public int x;
		
		public Pos() {
			this(0, 0);
		}
		
		public Pos(int y, int x) {
			this.y = y;
			this.x = x;
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
		
		public int compareTo(Node other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```