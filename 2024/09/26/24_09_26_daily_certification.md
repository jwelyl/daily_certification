# 24_09_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5931 **Cow Beauty Pageant**

[](https://www.acmicpc.net/problem/5931)

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
	
	private static char[][] map;
	private static boolean[][] visited;
	
	private static final Queue<int[]> q = new LinkedList<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		visited = new boolean[N][M];
		
		for(int y = 0; y < N; y++)
			map[y] = br.readLine().toCharArray();
		
		OUTER:
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++) {
				if(map[y][x] == 'X' && !visited[y][x]) {	//	첫 번째 문양 부분을 찾은 경우
					dfs(y, x);
					break OUTER;
				}
			}
		}
		
		System.out.println(bfs());
	} // main-end
	
	private static int bfs() {
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];
			int ccnt = cur[2];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int ncnt = ccnt + 1;
				
				if(isIn(ny, nx) && !visited[ny][nx]) {
					if(map[ny][nx] == '.') {
						visited[ny][nx] = true;
						q.offer(new int[] {ny, nx, ncnt});
					}
					else return ncnt - 1;
				}
			}
		}
		
		return -1;
	}
	
	private static void dfs(int y, int x) {
		visited[y][x] = true;
		q.offer(new int[] {y, x, 0});
		
		for(int d = 0; d < 4; d++) {
			int ny = y + dy[d];
			int nx = x + dx[d];
			
			if(isIn(ny, nx) && map[ny][nx] == 'X' && !visited[ny][nx])
				dfs(ny, nx);
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
} // Main-class-end
```