# 24_06_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16954 **움직이는 미로 탈출**

[](https://www.acmicpc.net/problem/16954)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final char[][][] map = new char[9][8][8];
	private static final boolean[][][] visited = new boolean[9][8][8];
	
	public static void main(String[] args) throws IOException {
		for(int r = 0; r < 8; r++)
			map[0][r] = br.readLine().toCharArray();
		
		//	time초일 때 map 상태
		for(int time = 1; time <= 8; time++) {
			for(int row = 7; row >= 1; row--) {
				for(int col = 0; col < 8; col++)
					map[time][row][col] = map[time - 1][row - 1][col];
			}
			
			Arrays.fill(map[time][0], '.');  
		}
        
		System.out.println(bfs() ? 1 : 0);
	}	//	main-end
	
	private static boolean bfs() {
		//	{시간, y좌표, x좌표} 넣기
		Queue<int[]> q = new LinkedList<>();
		q.offer(new int[] {0, 7, 0});

		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int ctime = cur[0] > 9 ? 8 : cur[0];	//	현재 시각, 8초 이후로는 맵이 변하지 않음
			int cy = cur[1];
			int cx = cur[2];	//	현재 위치
			
			if(map[ctime][cy][cx] == '#') //	만약 ctime - 1에 (cy, cx)로 이동한 다음에, ctime에 벽이 (cy, cx)로 내려오면
				continue;	//	(cy, cx)에서는 더 이상 이동 불가
			
			for(int d = 0; d < 9; d++) {
				int ntime = ctime + 1 > 8 ? 8 : ctime + 1;
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	현재 시각 ctime에 이동할 칸 (ny, nx)
			
				if(canGo(ny, nx, ctime) && !visited[ctime][ny][nx]) {
					if(ny == 0 && nx == 7)	//	도착 지점일 경우
						return true;
					
					visited[ctime][ny][nx] = true;
					q.offer(new int[] {ntime, ny, nx});
				}
			}
		}	//	while-end
		
		return false;
	}	//	bfs-end
	
	private static final int[] dy = {-1, -1, -1, 0, 1, 1, 1, 0, 0};
	private static final int[] dx = {-1, 0, 1, 1, 1, 0, -1, -1, 0};
	
	private static boolean canGo(int y, int x, int time) {
		return (0 <= y && y < 8) && (0 <= x && x < 8) && map[time][y][x] != '#';
	}
}	//	Main-class-ene
```