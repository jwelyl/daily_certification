# 24_11_12_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 1938 통나무 옮기기**

[](https://www.acmicpc.net/problem/1938)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static int N;
	private static char[][] map;
	//	visited[0][y][x] : 통나무가 가로 형태로 중심이 (y, x)일때 방문 여부
	//	visited[1][y][x] : 통나무가 세로 형태로 중심이 (y, x)일때 방문 여부
	private static boolean[][][] visited;
	
	private static int iLogY = -1;
	private static int iLogX = -1;			//	통나무 중심 위치
	private static int iLogStatus = -1;		//	통나무 상태 (0이면 가로, 1이면 세로)
	
	private static int endY = -1;
	private static int endX = -1;			//	도착점 중심 위치
	private static int endStatus = -1;		//	도착점 상태 (0이면 가로, 1이면 세로)
	
	public static void main(String[] args) throws IOException {
		int fLogY = -1;
		int fLogX = -1;	//	최초로 나타나는 통나무 위치
		int fEndY = -1;
		int fEndX = -1;	//	최초로 나타나는 도착점 위치
		
		N = Integer.parseInt(br.readLine());
		map = new char[N][N];
		visited = new boolean[2][N][N];
		
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < N; x++) {
				if(map[y][x] == 'B') {
					if(fLogY == -1 && fLogX == -1) {
						fLogY = y;
						fLogX = x;
					}
					else if(iLogY == -1 && iLogX == -1) {
						iLogY = y;
						iLogX = x;
					}
				}
				else if(map[y][x] == 'E') {
					if(fEndY == -1 && fEndX == -1) {
						fEndY = y;
						fEndX = x;
					}
					else if(endY == -1 && endX == -1) {
						endY = y;
						endX = x;
					}
				}
			}
		}
		
		if(fLogY == iLogY)
			iLogStatus = 0;
		else
			iLogStatus = 1;
		if(fEndY == endY)
			endStatus = 0;
		else
			endStatus = 1;
		
		System.out.println(bfs());
	}	//	main-end
	
	private static int bfs() {
		Queue<int[]> q = new LinkedList<>();
		visited[iLogStatus][iLogY][iLogX] = true;
		q.offer(new int[] {iLogY, iLogX, iLogStatus, 0});
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cstatus = cur[2];
			int ccnt = cur[3];
		
			int ny = cy;
			int nx = cx;
			int nstatus = cstatus;
			int ncnt = ccnt + 1;
			
			//	4방향 이동 시도
			for(int d = 0; d < 8; d += 2) {
				ny = cy + dy[d];
				nx = cx + dx[d];
				
				if(canMove(ny, nx, nstatus, d) && !visited[nstatus][ny][nx]) {	//	(ny, nx)가 중심이고 상태가 nstatus일때 d로 이동 가능하며, 방문하지 않은 경우
					if(ny == endY && nx == endX && nstatus == endStatus)	//	도착한 경우
						return ncnt;
					
					visited[nstatus][ny][nx] = true;
					q.offer(new int[] {ny, nx, nstatus, ncnt});
				}
			}
			
			//	회전 시도
			ny = cy;
			nx = cx;
			nstatus = turn(cstatus);
			
			if(canTurn(ny, nx) && !visited[nstatus][ny][nx]) {	//	회전이 가능하며, 방문하지 않은 경우
				if(ny == endY && nx == endX && nstatus == endStatus)	//	도착한 경우
					return ncnt;
				
				visited[nstatus][ny][nx] = true;
				q.offer(new int[] {ny, nx, nstatus, ncnt});
			}
		}
		
		return 0;	//	옮길 수 없는 경우
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	//	(y, x)가 범위 안에 있고 해당 칸에 나무가 없을 경우
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N) && map[y][x] != '1';
	}

	//	(y, x)를 중심으로 회전 가능하면 true
	private static boolean canTurn(int y, int x) {
		for(int d = 0; d < 8; d++) {
			//	주변 8칸 중 하나의 칸이라도 범위를 벗어나거나, 나무가 있을 경우
			if(!isIn(y + dy[d], x + dx[d]))
				return false;	//	회전 불가능
		}
		
		return true;
	}
	
	//	중심이 (y, x) 상태가 status인 통나무를 dir 방향으로 이동시킬 수 있는지 확인
	private static boolean canMove(int y, int x, int status, int dir) {
		if(status == 0) {
			int yl = y;
			int xl = x - 1;
			int yr = y;
			int xr = x + 1;
			
			if(!isIn(yl, xl) || !isIn(y, x) || !isIn(yr, xr))
				return false;
		}
		else {
			int yu = y - 1;
			int xu = x;
			int yd = y + 1;
			int xd = x;

			if(!isIn(yu, xu) || !isIn(y, x) || !isIn(yd, xd))
				return false;
		}
		
		return true;
	}
	
	private static int turn(int status) {
		return 1 - status;	//	0이면 1, 1이면 0 반환
	}
}	//	Main-class-end
```