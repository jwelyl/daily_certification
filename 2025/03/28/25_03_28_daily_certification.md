# 25_03_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26557 Bomb

[26557번: Bomb](http://boj.ma/26557/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int T;			//	테스트케이스 개수
	
	private static int F;	//	층 수
	private static int R;	//	한 층의 행 수
	private static int C;	//	한 층의 열 수
	
	private static char[][][] map;
	private static int sz;
	private static int sy;
	private static int sx;
	private static int ez;
	private static int ey;
	private static int ex;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			st = new StringTokenizer(br.readLine());
			F = Integer.parseInt(st.nextToken());
			R = Integer.parseInt(st.nextToken());
			C = Integer.parseInt(st.nextToken());
			
			map = new char[F][R][C];
			
			for(int f = 0; f < F; f++) {
				for(int r = 0; r < R; r++) {
					map[f][r] = br.readLine().toCharArray();
					for(int c = 0; c < C; c++) {
						if(map[f][r][c] == 'S') {
							sz = f;
							sy = r;
							sx = c;
						}
						else if(map[f][r][c] == 'E') {
							ez = f;
							ey = r;
							ex = c;
						}
					}
				}
			}
			
			sb.append(bfs()).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static int bfs() {
		Queue<int[]> q = new LinkedList<>();
		int[][][] dist = new int[F][R][C];
		for(int f = 0; f < F; f++) {
			for(int r = 0; r < R; r++)
				Arrays.fill(dist[f][r], Integer.MAX_VALUE);
		}
		
		dist[sz][sy][sx] = 0;
		q.offer(new int[] {sz, sy, sx, 0});
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cz = cur[0];
			int cy = cur[1];
			int cx = cur[2];
			int cc = cur[3];
			
			for(int d = 0; d < 6; d++) {
				int nz = cz + dz[d];
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nc = cc;
				
				if(isIn(nz, ny, nx)) {
					nc += map[nz][ny][nx] == '#' ? 1 : 0;
					
					if(nc < dist[nz][ny][nx]) {
						dist[nz][ny][nx] = nc;
						q.offer(new int[] {nz, ny, nx, nc});
					}
				}
			}
		}
		
		return dist[ez][ey][ex];
	}
	
	private static final int[] dz = {0, 0, 0, 0, -1, 1};
	private static final int[] dy = {0, 1, 0, -1, 0, 0};
	private static final int[] dx = {1, 0, -1, 0, 0, 0};
	
	private static boolean isIn(int z, int y, int x) {
		return (0 <= z && z < F) && (0 <= y && y < R) && (0 <= x && x < C);
	}
}	//	Main-class-end
```