# 25_05_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22956 소나기

[22956번: 소나기](http://boj.ma/22956/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int Q;
	
	private static int[][] heights;
	private static int[][] rained;
	private static int[] parents;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		heights = new int[N][M];
		rained = new int[N][M];
		parents = new int[N * M];
		for(int v = 0; v < N * M; v++)
			parents[v] = v;
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++)
				heights[y][x] = Integer.parseInt(st.nextToken());
		}
		
		for(int q = 1; q <= Q; q++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			int r = Integer.parseInt(st.nextToken());
			int v = y * M + x;
			
			for(int d = 0; d < 4; d++) {
				int ny = y + dy[d];
				int nx = x + dx[d];
				
				if(isIn(ny, nx) && rained[ny][nx] != 0) {
					int nv = find(ny * M + nx);
					union(find(v), nv);
				}
			}
			
			heights[y][x] -= r;
			rained[y][x] = q;
			
			int ay = -1;
			int ax = -1;
			
			int bv = find(v);
			int by = bv / M;
			int bx = bv % M;
			
			if(heights[by][bx] <= heights[y][x]) {
				parents[v] = bv;
				parents[bv] = bv;
				ay = by;
				ax = bx;
			}
			else {
				parents[bv] = v;
				parents[v] = v;
				ay = y;
				ax = x;
			}
			

			sb.append(ay + 1).append(" ").append(ax + 1).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};	

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}

	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		int y1 = v1 / M;
		int x1 = v1 % M;
		int y2 = v2 / M;
		int x2 = v2 % M;
		
		if(heights[y1][x1] < heights[y2][x2])
			parents[v2] = v1;
		else if(heights[y1][x1] == heights[y2][x2]) {
			if(rained[y1][x1] < rained[y2][x2])
				parents[v2] = v1;
			else
				parents[v1] = v2;
		}
		else
			parents[v1] = v2;
	}
}	//	Main-class-end
```