# 25_07_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22949 회전 미로 탐색

[22949번: 회전 미로 탐색](http://boj.ma/22949/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static int K;
	private static char[][] map;
	
	//	rotated[k][rot] : k(0 <= k < K^2)번째 영역의 rot번째 회전 상태
	private static char[][][][] rotated;
	
	//	visited[rot][y][x] : (y, x)가 속한 구역의 회전 상태가 rot일때 (y, x)에 도달한 최소 시간
	private static int[][][] visited;
 	
	private static int sy;
	private static int sx;
	private static int ey;
	private static int ex;
	
	public static void main(String[] args) throws IOException {
		K = Integer.parseInt(br.readLine());
		map = new char[4 * K][4 * K];
		rotated = new char[K * K][4][4][4];
		visited = new int[4][4 * K][4 * K];
		
		for(int rot = 0; rot < 4; rot++) {
			for(int y = 0; y < 4 * K; y++)
				Arrays.fill(visited[rot][y], NONE);
		}
		
		for(int y = 0; y < 4 * K; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < 4 * K; x++) {
				if(map[y][x] == 'S') {
					sy = y;
					sx = x;
				}
				else if(map[y][x] == 'E') {
					ey = y;
					ex = x;
				}
			}
		}
		
		setRotated();
		
		System.out.println(bfs());
	} //	main-end
	
	private static void setRotated() {
		for(int sectNum = 0; sectNum < K * K; sectNum++) {
			int sectRow = sectNum / K;
			int sectCol = sectNum % K;
			
			int sectY = sectRow * 4;
			int sectX = sectCol * 4;
			
			for(int y = 0; y < 4; y++) {
				for(int x = 0; x < 4; x++)
					rotated[sectNum][0][y][x] = map[y + sectY][x + sectX];
			}
			
			for(int rot = 1; rot < 4; rot++)
				rotate90(rotated[sectNum][rot - 1], rotated[sectNum][rot]);
		}
	}
	
	private static int bfs() {
		//	{회전 상태, 회전후 (y, x), 이동 시각}
		Queue<int[]> queue = new LinkedList<>();
		int res = NONE;
		
		visited[0][sy][sx] = 0;
		//	최초엔 회전 안한 상태로 (sy, sx)에 있음
		queue.offer(new int[] {0, sy, sx, 0});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int crot = cur[0];
			int cy = cur[1];
			int cx = cur[2];		//	현재 좌표 (cy, cx)
			int ccost = cur[3];		//	현재 좌표 (cy, cx)에 왔을때 회전 상태가 crot일때 최소 비용
			
			int csectNum = getSectNum(cy, cx);			//	현재 좌표 (cy, cx)가 속한 구역 번호
			
			for(int d = 0; d < 5; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	현재 좌표 (cy, cx)에서 주변 칸으로 이동할 칸
				
				if(!isIn(ny, nx))	//	범위를 벗어날 경우
					continue;
				
				int[] nnormalized = normalize(ny, nx);
				int nny = nnormalized[0];
				int nnx = nnormalized[1];			//	이동할 칸 (ny, nx)가 정규화된 칸
				int nsectNum = getSectNum(ny, nx);	//	이동할 칸 (ny, nx)가 속한 구역 번호
				
				//	(ny, nx)로 이동해도 같은 구역일 경우, (cy, cx)가 속한 구역이 한번 더 회전함
				//	(ny, nx)로 이동하면 다른 구역일 경우, 새로 이동한 구역이 한번 회전함
				int nrot = csectNum == nsectNum ? (crot + 1) % 4 : 1;
				
				char[][] nmap = rotated[nsectNum][csectNum == nsectNum ? crot : 0];
				
				if(nmap[nny][nnx] == '#')	//	이동할 칸이 벽인 경우
					continue;
				
				int[] nrotated = rotate90(ny, nx);
				int nry = nrotated[0];
				int nrx = nrotated[1];	//	(ny, nx)가 회전한 칸
				
				if(ccost + 1 < visited[nrot][nry][nrx]) {
					visited[nrot][nry][nrx] = ccost + 1;
					queue.offer(new int[] {nrot, nry, nrx, ccost + 1});
				}
			}
		}
		
		for(int rot = 0; rot < 4; rot++) {
		    int[] rotated = rotate90(ey, ex, rot);  // rot만큼 회전된 좌표
		    int ry = rotated[0];
		    int rx = rotated[1];
		    res = Math.min(res, visited[rot][ry][rx]);
		}
		
		return res == NONE ? -1 : res;
	}
	
	//	(y, x)를 (yy, xx) (0 <= yy, xx < 4)로 정규화시킴 
	private static int[] normalize(int y, int x) {
		int sectNum = getSectNum(y, x);
		int sectRow = sectNum / K;
		int sectCol = sectNum % K;
		int sectY = sectRow * 4;
		int sectX = sectCol * 4;
		int yy = y - sectY;
		int xx = x - sectX;
		
		return new int[] {yy, xx};
	}
	
	private static int[] rotate90(int y, int x) {
		int[] normalized = normalize(y, x);
		int ny = normalized[0];
		int nx = normalized[1];
		
		int ry = nx; 
		int rx = 3 - ny;
		
		int sectNum = getSectNum(y, x);
		int sectRow = sectNum / K;
		int sectCol = sectNum % K;
		int sectY = sectRow * 4;
		int sectX = sectCol * 4;
		
		return new int[] {sectY + ry, sectX + rx};
	}
	
	private static int[] rotate90(int y, int x, int cnt) {
		int resY = y;
		int resX = x;
		
		for(int i = 0; i < cnt; i++) {
			int[] rotated = rotate90(resY, resX);
			resY = rotated[0];
			resX = rotated[1];
		}
		
		return new int[] {resY, resX};
	}
	
	private static void rotate90(char[][] before, char[][] after) {
		for(int y = 0; y < 4; y++) {
			for(int x = 0; x < 4; x++)
				after[x][3 - y] = before[y][x];
		}
	}
	
	//	(y, x)가 속한 구역 번호
	private static int getSectNum(int y, int x) {
		int yq = y / 4;
		int xq = x / 4;
		
		return K * yq + xq;
	}
	
	private static final int[] dy = {0, 0, 1, 0, -1};
	private static final int[] dx = {0, 1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < 4 * K) && (0 <= x && x < 4 * K);
	}
} //	Main-class-end
```