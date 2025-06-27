# 25_06_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1473 미로 탈출

[1473번: 미로 탈출](http://boj.ma/1473/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	 
	private static char[][] board;
	//	visited[y][x][rowStat][colStat] : 현재 위치가 (y, x), 행 회전 상태가 rowStat, 열 회전 상태가 colStat일때 최소 이동 횟수
	//	A/B <-> A/B
	//	C <-> D
	//	어떤 칸이던 2번 회전하면 자기자신으로 돌아옴
	//	특정 행/열의 회전상태는 0/1로 나타낼 수 있음
	//	rowStat : N비트 정수, y행이 회전 상태면 y번째 bit = 1
	//	colStat : M비트 정수, x열이 회전 상태면 x번째 bit = 1
	//	(y, x)칸의 회전횟수 = rowStat의 y번째 비트 + colStat의 x번째 비트
	private static int[][][][] visited;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		board = new char[N][M];
		visited = new int[N][M][1 << N][1 << M];
		
		for(int y = 0; y < N; y++) {
			board[y] = br.readLine().toCharArray();
			for(int x = 0; x < M; x++) {
				for(int yy = 0; yy < (1 << N); yy++)
					Arrays.fill(visited[y][x][yy], NONE);
			}
		}
		
		System.out.println(bfs());
	} //	main-end
	
	private static int bfs() {
		int res = NONE;
		
		//	{y, x, rowStat, colStat, cost}
		Queue<int[]> queue = new LinkedList<>();
		visited[0][0][0][0] = 0;
		queue.offer(new int[] {0, 0, 0, 0, 0});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int crowStat = cur[2];
			int ccolStat = cur[3];
			int cc = cur[4];
			char croomStat = getRoomStat(cy, cx, crowStat, ccolStat);
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nrowStat = crowStat;
				int ncolStat = ccolStat;

				if(!isIn(ny, nx))
					continue;
				
				char nroomStat = getRoomStat(ny, nx, nrowStat, ncolStat);
				
				if(d % 2 == 0) {	//	좌우로 움직일 경우
					if((croomStat == 'A' || croomStat == 'D') && (nroomStat == 'A' || nroomStat == 'D')) {	//	회전 없이 좌우로 왕래가 가능할 경우
						if(cc + 1 < visited[ny][nx][nrowStat][ncolStat]) {	//	이동 비용 1
							visited[ny][nx][nrowStat][ncolStat] = cc + 1;
							queue.offer(new int[] {ny, nx, nrowStat, ncolStat, cc + 1});
						}
					}
				}
				else {
					if((croomStat == 'A' || croomStat == 'C') && (nroomStat == 'A' || nroomStat == 'C')) {	//	회전 없이 상하로 왕래가 가능할 경우
						if(cc + 1 < visited[ny][nx][nrowStat][ncolStat]) {	//	이동 비용 1
							visited[ny][nx][nrowStat][ncolStat] = cc + 1;
							queue.offer(new int[] {ny, nx, nrowStat, ncolStat, cc + 1});
						}
					}
				}
			}
			
			//	회전시키기
			crowStat = crowStat ^ (1 << cy);
			ccolStat = ccolStat ^ (1 << cx);
			
			if(cc + 1 < visited[cy][cx][crowStat][ccolStat]) {	//	회전 비용 1
				visited[cy][cx][crowStat][ccolStat] = cc + 1;
				queue.offer(new int[] {cy, cx, crowStat, ccolStat, cc + 1});
			}
		}
 		
		for(int y = 0; y < (1 << N); y++) {
			for(int x = 0; x < (1 << M); x++)
				res = Math.min(res, visited[N - 1][M - 1][y][x]);
		}
		
		return res == NONE ? -1 : res;	//	(N - 1, M - 1)로 도착할 수 없는 경우
	}

	//	(y, x) 방의 행 회전 상태가 rowStat, 열 회전 상태가 colStat일때의 상태
	private static char getRoomStat(int y, int x, int rowStat, int colStat) {
		char ch = board[y][x];	//	초기 방 상태
		
		int rowCnt = (rowStat & (1 << y)) == 0 ? 0 : 1;	//	열 회전 횟수(0/1)
		int colCnt = (colStat & (1 << x)) == 0 ? 0 : 1;	//	행 회전 횟수(0/1) 
	
		if(ch == 'A' || ch == 'B')
			return ch;
		
		return (rowCnt + colCnt) % 2 == 1 ? (ch == 'C' ? 'D' : 'C') : ch;
	}
	
	//	R, D, L, U
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
} //	Main-class-end
```