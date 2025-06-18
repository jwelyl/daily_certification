# 25_06_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5022 연결

[5022번: 연결](http://boj.ma/5022/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static int ay1;
	private static int ax1;
	private static int ay2;
	private static int ax2;
	private static int by1;
	private static int bx1;
	private static int by2;
	private static int bx2;
	
	private static boolean[][] visited;
	private static int[][][] prevs;
	
	private static int dist1;
	private static int dist2;
	
	private static int answer = INF;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		ay1 = Integer.parseInt(st.nextToken());
		ax1 = Integer.parseInt(st.nextToken());
		st = new StringTokenizer(br.readLine());
		ay2 = Integer.parseInt(st.nextToken());
		ax2 = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		by1 = Integer.parseInt(st.nextToken());
		bx1 = Integer.parseInt(st.nextToken());
		st = new StringTokenizer(br.readLine());
		by2 = Integer.parseInt(st.nextToken());
		bx2 = Integer.parseInt(st.nextToken());
	
		visited = new boolean[N + 1][M + 1];
		prevs = new int[N + 1][M + 1][2];
		
		//	1. (ay1, ax1)에서 (ay2, ax2)로 최단거리로 먼저 간 후, 그 경로를 피해서 (by1, bx1)에서 (by2, bx2)로 가보기
		visited[by1][bx1] = true;
		visited[by2][bx2] = true;
		dist1 = bfs(ay1, ax1, ay2, ax2, true);		//	1-1. (ay1, ax1)에서 (ay2, ax2)로는 아무 제약 없이 이동 
		dist2 = bfs(by1, bx1, by2, bx2, false);		//	1-2. (by1, bx1)에서 (by2, bx2)로 이동할 경우에는 1-1의 경로를 피해서 이동
		
		if(dist2 != INF)
			answer = Math.min(answer, dist1 + dist2);
		
		//	2. (by1, bx1)에서 (by2, bx2)로 최단거리로 먼저 간 후, 그 경로를 피해서 (ay1, ax1)에서 (ay2, ax2)로 가보기
		visited[ay1][ax1] = true;
		visited[ay2][ax2] = true;
		dist1 = bfs(by1, bx1, by2, bx2, true);		//	2-1. (by1, bx1)에서 (by2, bx2)로는 아무 제약 없이 이동 
		dist2 = bfs(ay1, ax1, ay2, ax2, false);		//	2-2. (ay1, ax1)에서 (ay2, ax2)로 이동할 경우에는 2-1의 경로를 피해서 이동
		
		if(dist2 != INF)
			answer = Math.min(answer, dist1 + dist2);
		
		System.out.println(answer == INF ? "IMPOSSIBLE" : answer);
	} // main-end
	
	private static int bfs(int fromY, int fromX, int toY, int toX, boolean isFirst) {
		int res = INF;
		Queue<int[]> queue = new LinkedList<>();
		visited[fromY][fromX] = true;
		queue.offer(new int[] {fromY, fromX, 0});
		
		WHILE:
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && !visited[ny][nx]) {
					prevs[ny][nx][0] = cy;
					prevs[ny][nx][1] = cx;
					visited[ny][nx] = true;
					
					if(ny == toY && nx == toX) {
						res = cc + 1;
						break WHILE;
					}
					
					queue.offer(new int[] {ny, nx, cc + 1});
				}
			}
		}

		clear();	//	다음 BFS를 위해 방문 기록 초기화
		
		if(isFirst) {	//	첫번째 BFS의 경우 두번째 BFS를 위해 최단경로에 해당하는 칸들을 방문 처리해야 함
			int curY = toY;
			int curX = toX;
			
			while(true) {
				visited[curY][curX] = true;	//	최단경로에 포함된 칸만 방문 처리
				
				if(curY == fromY && curX == fromX)
					break;
				
				int prevY = prevs[curY][curX][0];
				int prevX = prevs[curY][curX][1];
				
				curY = prevY;
				curX = prevX;
			}
		}
		
		return res;
	}
	
	private static void clear() {
		for(int y = 0; y <= N; y++)
			Arrays.fill(visited[y], false);
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y <= N) && (0 <= x && x <= M);
	}
} // Main-class-end
```