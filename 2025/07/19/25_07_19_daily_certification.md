# 25_07_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22944 죽음의 비

[22944번: 죽음의 비](http://boj.ma/22944/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final char S = 'S';	//	시작점
	private static final char E = 'E';	//	도착점 (안전지대)
	private static final char U = 'U';	//	우산
	private static final char R = '.';	//	비
	
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int H;	//	초기 체력
	private static int D;	//	우산 내구도
	
	private static char[][] map;
	private static int[][] visited;
	private static int sy;
	private static int sx;	//	시작 위치 (sy, sx)
	
	private static int answer = INF;	//	안전지대까지 도착하는 최소 이동 횟수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		H = Integer.parseInt(st.nextToken());
		D = Integer.parseInt(st.nextToken());
		
		map = new char[N][N];
		visited = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < N; x++) {
				if(map[y][x] == S) {
					sy = y;
					sx = x;
				}
			}
		}
	
		bfs();
	}	//	main-end

	private static void bfs() {
		Queue<int[]> queue = new LinkedList<>();
		visited[sy][sx] = H;
		queue.offer(new int[] {sy, sx, H, 0, 0});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int ch = cur[2];
			int cd = cur[3];
			int cc = cur[4];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nh = ch;
				int nd = cd;
				int nc = cc + 1;
				
				if(!isIn(ny, nx))
					continue;
				
				if(map[ny][nx] == E) {
					answer = Math.min(answer, nc);
					continue;
				}
				
				if(map[ny][nx] == U) {
					nd = D - 1;
					
					if(visited[ny][nx] < nh + nd) {
						visited[ny][nx] = nh + nd;
						queue.offer(new int[] {ny, nx, nh, nd, nc});
					}
				}
				else {
					if(nd > 0)
						nd--;
					else
						nh--;
					
					if(nh == 0)
						continue;
					
					if(visited[ny][nx] < nh + nd) {
						visited[ny][nx] = nh + nd;
						queue.offer(new int[] {ny, nx, nh, nd, nc});
					}
				}
			}
		}
		
		System.out.println(answer == INF ? -1 : answer);
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```