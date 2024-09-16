# 24_09_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17267 상남자

[](https://www.acmicpc.net/problem/17267)

### Priority Queue

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	private static final char WALL = '1';
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;	//	행 수
	private static int M;	//	열 수
	
	private static int L;	//	왼쪽으로 이동할 수 있는 최대 이동 수
	private static int R;	//	왼쪽으로 이동할 수 있는 최대 이동 수
	
	private static char[][] map;			//	맵
	private static boolean[][] visited;		//	방문 처리
	
	private static int sy = 0;
	private static int sx = 0;	//	처음 시작 좌표
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		
		L = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		visited = new boolean[N][M];
		for(int i = 0; i < N; i++) {
			map[i] = br.readLine().toCharArray();
			for(int j = 0; j < M; j++) {
				if(map[i][j] == '2') {
					sy = i;
					sx = j;
				}
			}
		}
		
		System.out.println(bfs());
	}	//	main-end
	
	private static int bfs() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		int ans = 1;	//	처음 있는 칸
		visited[sy][sx] = true;
		pq.offer(new Node(sy, sx, L, R));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cy = cur.y;
			int cx = cur.x;
			int cl = cur.l;
			int cr = cur.r;
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nl = cl;
				int nr = cr;
				
				if(isIn(ny, nx) && map[ny][nx] != WALL && !visited[ny][nx]) {
					if(d == 0) {	//	오른쪽으로 갈 경우
						if(cr != 0) {	//	오른쪽으로 갈 수 있을 경우
							nr--;
							visited[ny][nx] = true;
							ans++;
							pq.offer(new Node(ny, nx, nl, nr));
						}
					}
					else if(d == 2) {	//	왼쪽으로 갈 경우
						if(cl != 0) {	//	왼쪽으로 갈 수 있을 경우
							nl--;
							visited[ny][nx] = true;
							ans++;
							pq.offer(new Node(ny, nx, nl, nr));
						}
					}
					else {	//	위아래로 갈 경우
						visited[ny][nx] = true;
						ans++;
						pq.offer(new Node(ny, nx, nl, nr));
					}
				}
			}
		}
		
		return ans;
	}	//	bfs-end
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	private static class Node implements Comparable<Node> {
		public int y;
		public int x;
		public int l;
		public int r;
		
		public Node(int y, int x, int l, int r) {
			this.y = y;
			this.x = x;
			this.l = l;
			this.r = r;
		}
		
		@Override
		public int compareTo(Node other) {	//	왼쪽, 오른쪽으로 이동할 수 있는 칸 개수가 많은 것부터
			return Integer.compare(other.l + other.r, this.l + this.r); 
		}
	}
}	//	Main-class-end
```

### 0-1 BFS

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	private static final char WALL = '1';
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;	//	행 수
	private static int M;	//	열 수
	
	private static int L;	//	왼쪽으로 이동할 수 있는 최대 이동 수
	private static int R;	//	왼쪽으로 이동할 수 있는 최대 이동 수
	
	private static char[][] map;			//	맵
	private static int[][] visited;		//	방문 처리
	
	private static int sy = 0;
	private static int sx = 0;	//	처음 시작 좌표
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		
		L = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		visited = new int[N][M];
		for(int i = 0; i < N; i++) {
			Arrays.fill(visited[i], NONE);
			
			map[i] = br.readLine().toCharArray();
			for(int j = 0; j < M; j++) {
				if(map[i][j] == '2') {
					sy = i;
					sx = j;
				}
			}
		}
		
		System.out.println(bfs());
	}	//	main-end
	
	private static int bfs() {
		Queue<int[]> q = new LinkedList<>();
		
		visited[sy][sx] = 0;
		q.offer(new int[] {sy, sx, visited[sy][sx], L, R});

		while(!q.isEmpty()) {	
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];	//	현재 좌표
			int cd = cur[2];	//	현재 좌표까지 오는 최적 거리
			int cl = cur[3];	//	현재 좌표에서 더 왼쪽으로 갈 수 있는 칸의 개수
			int cr = cur[4];	//	현재 좌표에서 더 오른쪽으로 갈 수 있는 칸의 개수
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	다음 칸 좌표
				int nd = cd;
				
				if(isIn(ny, nx) && map[ny][nx] != WALL) {	//	범위를 벗어나지 않고, 벽도 아니고, 방문하지도 않았거나 더 최적으로 올 수 있는 경우
					int nl = cl;
					int nr = cr;
					
					if(d % 2 == 1) {	//	위아래로 움직이는 경우, 추가 비용 없이 무조건 이동 가능
						if(nd < visited[ny][nx]) {
							visited[ny][nx] = nd;
							q.offer(new int[] {ny, nx, visited[ny][nx], nl, nr});
						}
					}
					else {
						if(d == 2) {	//	왼쪽으로 이동하는 경우
							nl--;
							
							if(nl < 0)	//	왼쪽으로 더 이동이 불가능한 경우
								continue;
							
							nd++;	//	추가 비용 1 필요
							
							if(nd < visited[ny][nx]) {
								visited[ny][nx] = nd;
								q.offer(new int[] {ny, nx, visited[ny][nx], nl, nr});
							}
						}
						else {	//	오른쪽으로 이동하는 경우
							nr--;
							
							if(nr < 0)	//	오른쪽으로 더 이동이 불가능한 경우
								continue;
							
							nd++;	//	추가 비용 1 필요
							
							if(nd < visited[ny][nx]) {
								visited[ny][nx] = nd;
								q.offer(new int[] {ny, nx, visited[ny][nx], nl, nr});
							}
						}
					}
				}
			}
		}
		
		int ans = 0;
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < M; c++)
				if(visited[r][c] != NONE)
					ans++;
		}
		
		return ans;
	}	//	bfs-end
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```