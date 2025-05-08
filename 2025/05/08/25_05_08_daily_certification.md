# 25_05_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5846 Tractor

[5846번: Tractor](http://boj.ma/5846/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	
	private static int[][] map;
	private static int[] parents;
	private static int[] dsCnts;
	
	private static int maxHeight = -1;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		map = new int[N][N];
		parents = new int[N * N];
		dsCnts = new int[N * N];

		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				map[y][x] = Integer.parseInt(st.nextToken());
				
				maxHeight = Math.max(maxHeight, map[y][x]);
			}
		}
		
		System.out.println(parametricSearch());
	} //	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = maxHeight;
		int res = maxHeight;
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(ok(mid)) {
				res =  mid;
				end = mid - 1;
			}
			else start = mid + 1;
		}
		
		return res;
	}
	
	private static boolean ok(int limit) {
		for(int v = 0; v < N * N; v++) {
			parents[v] = v;
			dsCnts[v] = 1;
		}
		
		boolean[][] visited = new boolean[N][N];
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++) {
				if(!visited[y][x])
					dfs(y, x, visited, limit);
			}
		}
		
		for(int v = 0; v < N * N; v++) {
			if(dsCnts[v] >= (N * N + 1) / 2)
				return true;
		}
		
		return false;
	}
	
	private static void dfs(int y, int x, boolean[][] visited, int limit) {
		int cv = y * N + x;
		visited[y][x] = true;
		
		for(int d = 0; d < 4; d++) {
			int ny = y + dy[d];
			int nx = x + dx[d];
			
			if(isIn(ny, nx) && Math.abs(map[ny][nx] - map[y][x]) <= limit && !visited[ny][nx]) {
				int nv = ny * N + nx;
				union(cv, nv);
				dfs(ny, nx, visited, limit);
			}
		}
	}
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			parents[v2] = v1;
			dsCnts[v1] += dsCnts[v2];
			dsCnts[v2] = 0;
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
} //	Main-class-end
```