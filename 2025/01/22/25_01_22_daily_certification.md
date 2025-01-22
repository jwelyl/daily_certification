# 25_01_22_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 18128** **치삼이의 징검다리 건너기**

[18128번: 치삼이의 징검다리 건너기](https://boj.ma/18128/t)

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
	private static StringTokenizer st;

	private static int N;
	private static int W;
	
	private static char[][] map;
	private static int[][] waterTimes;		//	waterTimes[y][x] : (y, x)에 물이 도착하는 시간
	private static boolean[][] visited;		//	BFS 방문 배열

	private static final Queue<int[]> queue = new LinkedList<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		W = Integer.parseInt(st.nextToken());
		
		map = new char[N][N];
		waterTimes = new int[N][N];
		visited = new boolean[N][N];
		
		for(int y = 0; y < N; y++)
			Arrays.fill(waterTimes[y], N * N + 1);
		
		for(int w = 0; w < W; w++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			
			visited[y][x] = true;
			waterTimes[y][x] = 0;
			queue.offer(new int[] {y, x, waterTimes[y][x]});
		}
		
		for(int y = 0; y < N; y++)
			map[y] = br.readLine().toCharArray();
			
		waterBfs();	//	물이 퍼짐
		
		System.out.println(parametricSearch());
	} //	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = N * N + 1;
		int res = -1;
		
		while(start <= end) {
			int mid = (start + end) / 2;	//	mid 안에 도착할 수 있는지 체크
			
			if(bfs(mid)) {	//	mid 안에 도착할 수 있을 경우
				res = mid;	//	일단 mid 저장
				end = mid - 1;	//	더 빠른 시간 안에 도착할 수 있나 체크
			}
			else	//	mid 안에 도착할 수 없을 경우
				start = mid + 1;	//	더 많은 시간 안에 도착할 수 있나 체크
		}
		
		return res;
	}
	
	//	제한시간 limit 안에 (0, 0)에서 (N - 1, N - 1)에 도달 가능한지 체크
	private static boolean bfs(int limit) {
		clear();
		visited[0][0] = true;
		queue.offer(new int[] {0, 0, 0});	//	(0, 0)에서 0일차에 출발
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];	//	(cy, cx)에
			int cd = cur[2];	//	cd 일에 도착
			
			for(int d = 0; d < 8; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	(ny, nx)에
				
				if(isIn(ny, nx) && map[ny][nx] == '1' && !visited[ny][nx]) {
					if(ny == N - 1 && nx == N - 1) {	//	(N-1, N-1)은 물 없어도 갈 수 있음, cd에 도착하게 된다.
						if(cd <= limit)
							return true;
					}
					else {
						if(cd >= waterTimes[ny][nx])	//	물이 찬 이후에 도착하게 될 경우
							queue.offer(new int[] {ny, nx, cd});
						else {	//	물이 차기 전에 도착하게 될 경우
							if(waterTimes[ny][nx] > limit)	//	물이 찰때까지 기다릴 수 없을 경우
								continue;
							queue.offer(new int[] {ny, nx, waterTimes[ny][nx]});	//	물이 찰때 딱 맞춰서 감
						}
						visited[ny][nx] = true;
					}
				}
			}
		}
		
		return false;
	}
	
	private static void clear() {
		for(int y = 0; y < N; y++)
			Arrays.fill(visited[y], false);
		queue.clear();
	}

	private static void waterBfs() {
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int ctime = cur[2];	//	(cy, cx)에 물이 오는 최소 시간
			
			for(int d = 0; d < 8; d += 2) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && !visited[ny][nx]) {
					waterTimes[ny][nx] = ctime + 1;
					visited[ny][nx] = true;
					queue.offer(new int[] {ny, nx, waterTimes[ny][nx]});
				}
			}
		}
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
} //	Main-class-end
```