# 24_09_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15806 **영우의 기숙사 청소**

[](https://www.acmicpc.net/problem/15806)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K; 
	private static int T;
	
	private static Queue<Point> queue = new LinkedList<>();
	private static boolean[][] check;
	private static boolean[][][] room;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		if(N == M && M == K && K == 1) {
			System.out.println("NO");
			return;
		}
		
		check = new boolean[N][N];
		room = new boolean[N][N][2];
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			
			queue.add(new Point(y, x, 0));
			room[y][x][0] = true;
		}
		
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			
			check[y][x] = true;
		}
		
		bfs();
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				if(check[r][c] && room[r][c][T]) {
					System.out.println("YES");
					return;
				}
			}
		}
		
		System.out.println("NO");
	}	//	main-end
	
	private static void bfs() {
		while(!queue.isEmpty()) {
			Point cur = queue.poll();
			int cy = cur.y;
			int cx = cur.x;
			int cd = cur.d;
			
			if(cd == T) 
				continue;
			
			int nd = cd + 1;
			
			for(int d = 0; d < 8; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(!isIn(ny, nx) || room[ny][nx][nd & 1]) 
					continue;
				
				queue.add(new Point(ny, nx, nd));
				room[ny][nx][nd & 1] = true;
			}
		}
		
		T &= 1;
	}
	
	private static final int[] dy = {1, 2, 2, 1, -1, -2, -2, -1};
	private static final int[] dx = {2, 1, -1, -2, -2, -1, 1, 2};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
	
	private static class Point {
		public int y;
		public int x;
		public int d;
		
		public Point(int r, int c, int d) {
			this.y = r;
			this.x = c;
			this.d = d;
		}
	}
}	//	Main-class-end
```