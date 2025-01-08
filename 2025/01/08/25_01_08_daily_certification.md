# 25_01_08_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 14499 주사위 굴리기**

[14499번: 주사위 굴리기](https://boj.ma/14499/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int CEIL = 0;		//	천장	
	private static final int EAST = 1;		//	동쪽
	private static final int WEST = 2;		//	서쪽
	private static final int NORTH = 3;		//	북
	private static final int SOUTH = 4;		//	남쪽
	private static final int FLOOR = 5;		//	바닥
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K;
	private static int[][] map;
	
	//	diceValues[nth] : 주사위의 nth번째 면에 적힌 수 
	private static final int[] diceValues = {0, 0, 0, 0, 0, 0, 0};
	//	diceDir[nth] : 주사위의 nth번째 면이 바라보는 방향
	//	최초 방향 : 1 -> 천장, 2 -> 북, 3 -> 동, 4 -> 서, 5 -> 남, 6 -> 바닥 
	private static final int[] diceDirs = {0, CEIL, NORTH, EAST, WEST, SOUTH, FLOOR};
	
	private static int diceX = 0;
	private static int diceY = 0;	//	주사위 밑면 위치
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		diceX = Integer.parseInt(st.nextToken());
		diceY = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		map = new int[N][M];
		for(int x = 0; x < N; x++) {
			st = new StringTokenizer(br.readLine());
			for(int y = 0; y < M; y++)
				map[x][y] = Integer.parseInt(st.nextToken());
		}
		
		st = new StringTokenizer(br.readLine());
		for(int q = 0; q < K; q++) {
			int dir = Integer.parseInt(st.nextToken());		//	주사위 굴릴 방향
		
			if(!isIn(diceX + dx[dir], diceY + dy[dir]))		//	주사위를 dir 방향으로 굴렸을때 범위를 벗어날 경우
				continue;	//	무시

			roll(dir);	//	dir 방향으로 굴리기
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void roll(int dir) {
		int nx = diceX + dx[dir];
		int ny = diceY + dy[dir];	//	주사위가 이동할 칸
		
		int value = map[nx][ny];	//	이동할 칸의 맵의 값

		int eastSide = 0;	//	현재 동쪽 바라보는 면
		int westSide = 0;	//	현재 서쪽 바라보는 면
		int northSide = 0;	//	현재 북쪽 바라보는 면
		int southSide = 0;	//	현재 남쪽 바라보는 면
		int ceilSide = 0;	//	현재 천장 바라보는 면
		int floorSide = 0;	//	현재 바닥 바라보는 면
		
		if(dir == EAST || dir == WEST) {	//	동쪽 또는 서쪽으로 이동할 경우
			for(int side = 1; side <= 6; side++) {
				if(diceDirs[side] == EAST) 
					eastSide = side;
				else if(diceDirs[side] == WEST)
					westSide = side;
				else if(diceDirs[side] == CEIL)
					ceilSide = side;
				else if(diceDirs[side] == FLOOR)
					floorSide = side;
			}
			
			if(dir == EAST) {	//	동쪽으로 이동할 경우
				diceDirs[eastSide] = FLOOR;
				diceDirs[westSide] = CEIL;
				diceDirs[ceilSide] = EAST;
				diceDirs[floorSide] = WEST;
				
				if(value == 0)	//	가려는 칸의 값이 0일 경우
					map[nx][ny] = diceValues[eastSide];	//	바닥칸의 값이 가려는 칸에 복사됨
				else {
					diceValues[eastSide] = map[nx][ny];
					map[nx][ny] = 0;
				}
				
				sb.append(diceValues[westSide]).append("\n");
			} 
			else {	//	서쪽으로 이동할 경우
				diceDirs[eastSide] = CEIL;
				diceDirs[westSide] = FLOOR;
				diceDirs[ceilSide] = WEST;
				diceDirs[floorSide] = EAST;
				
				if(value == 0)	//	가려는 칸의 값이 0일 경우
					map[nx][ny] = diceValues[westSide];	//	바닥칸의 값이 가려는 칸에 복사됨
				else {
					diceValues[westSide] = map[nx][ny];
					map[nx][ny] = 0;
				}
				
				sb.append(diceValues[eastSide]).append("\n");
			}
		}
		else {	//	남쪽 또는 북쪽으로 이동할 경우
			for(int side = 1; side <= 6; side++) {
				if(diceDirs[side] == NORTH) 
					northSide = side;
				else if(diceDirs[side] == SOUTH)
					southSide = side;
				else if(diceDirs[side] == CEIL)
					ceilSide = side;
				else if(diceDirs[side] == FLOOR)
					floorSide = side;
			}
			
			if(dir == NORTH) {	//	북쪽으로 이동할 경우
				diceDirs[northSide] = FLOOR;
				diceDirs[southSide] = CEIL;
				diceDirs[ceilSide] = NORTH;
				diceDirs[floorSide] = SOUTH;
				
				if(value == 0)	//	가려는 칸의 값이 0일 경우
					map[nx][ny] = diceValues[northSide];	//	바닥칸의 값이 가려는 칸에 복사됨
				else {
					diceValues[northSide] = map[nx][ny];
					map[nx][ny] = 0;
				}
				
				sb.append(diceValues[southSide]).append("\n");
			} 
			else {	//	남쪽으로 이동할 경우
				diceDirs[northSide] = CEIL;
				diceDirs[southSide] = FLOOR;
				diceDirs[ceilSide] = SOUTH;
				diceDirs[floorSide] = NORTH;
				
				if(value == 0)	//	가려는 칸의 값이 0일 경우
					map[nx][ny] = diceValues[southSide];	//	바닥칸의 값이 가려는 칸에 복사됨
				else {
					diceValues[southSide] = map[nx][ny];
					map[nx][ny] = 0;
				}
				
				sb.append(diceValues[northSide]).append("\n");
			}
		}
		
		diceX = nx;
		diceY = ny;
	}
	
	//	맵에 대하여 천장(0), 동(1), 서(2), 북(3), 남(4), 바닥(5) 
	private static final int[] dx = {0, 0, 0, -1, 1, 0};
	private static final int[] dy = {0, 1, -1, 0, 0, 0};
	
	private static boolean isIn(int x, int y) {
		return (0 <= x && x < N) && (0 <= y && y < M);
	}
}	//	Main-class-end
```

### **BOJ 13460 구슬 탈출 2**

[13460번: 구슬 탈출 2](https://boj.ma/13460/t)

**BFS**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br =  new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static char[][] map;
	private static boolean[][][][] visited;	//	visited[ry][rx][by][bx] : 빨간 구슬이 (ry, rx)이고 파란 구슬이 (by, bx)로 방문한 적이 있으면 true
	
	private static int ry = 0;
	private static int rx = 0;	//	최초 빨간 구슬 위치
	private static int by = 0;
	private static int bx = 0;	//	최초 파란 구슬 위치
	private static int hy = 0;
	private static int hx = 0;	//	구멍 위치
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		visited = new boolean[N][M][N][M];
		
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < M; x++) {
				if(map[y][x] == 'R') {
					ry = y;
					rx = x;
				}
				else if(map[y][x] == 'B') {
					by = y;
					bx = x;
				}
				if(map[y][x] == 'O') {
					hy = y;
					hx = x;
				}
			}
		}
		
		System.out.println(bfs());
	}	//	main-end
	
	private static int bfs() {
		Queue<int[]> queue = new LinkedList<>();
		visited[ry][rx][by][bx] = true;
		queue.offer(new int[] {ry, rx, by, bx, 0});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cry = cur[0];
			int crx = cur[1];		//	현재 빨간 구슬 위치
			int cby = cur[2];
			int cbx = cur[3];		//	현재 파란 구슬 위치
			int ccnt = cur[4];		//	현재까지 기울인 횟수
			
			for(int dir = 0; dir < 4; dir++) {	//	dir 방향으로 기울여보기
				int[] next = move(cry, crx, cby, cbx, dir);
				int nry = next[0];
				int nrx = next[1];		//	기울인 결과 빨간 구슬 위치
				int nby = next[2];
				int nbx = next[3];		//	기울인 결과 파란 구슬 위치
				int ncnt = ccnt + 1;	//	기울인 횟수
				
				if(nby == hy && nbx == hx)	//	빨간 구슬에 상관없이 파란 구슬이 구멍에 들어간 경우, 무효
					continue;
				//	빨간 구슬은 구멍에 들어가고, 파란 구슬은 들어가지 않은 경우, 조건 만족
				if((nry == hy && nrx == hx) && !(nby == hy && nbx == hx))
					return ncnt;
				
				//	아직 방문하지 않았고 이번 방문 후에도 더 기울일 기회가 남았을 경우
				if(!visited[nry][nrx][nby][nbx] && ncnt < 10) {
					visited[nry][nrx][nby][nbx] = true;
					queue.offer(new int[] {nry, nrx, nby, nbx, ncnt});
				}
			}
		}
		
		return -1;	//	10번 이하로 기울여서 도달할 수 없을 경우
	}
	
	private static int[] move(int ry, int rx, int by, int bx, int dir) {
		int[] res = new int[4];
		int compare = compare(ry, rx, by, bx, dir);
		
		int nry = ry;
		int nrx = rx;	//	빨간 구슬이 최종적으로 이동할 위치
		int nby = by;
		int nbx = bx;	//	파란 구슬이 최종적으로 이동할 위치
		
		if(compare < 0) {	//	파란 구슬 먼저 이동할 경우
			//	파란 구슬 먼저 위치 정하기
			while(true) {
				if(map[nby][nbx] == '#') {	//	벽에 가로막힌 경우
					nby -= dy[dir];
					nbx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				else if(map[nby][nbx] == 'O')	//	파란 구슬이 구멍을 만난 경우
					return new int[] {nry, nrx, nby, nbx};	//	어차피 파란 구슬이 구멍에 빠진 순간 빨간 구슬은 의미 없음
			
				nby += dy[dir];
				nbx += dx[dir];	//	다음 칸으로 이동
			}
			
			//	빨간 구슬 이동시키기
			while(true) {
				if(map[nry][nrx] == 'O')	//	빨간 구슬이 구멍을 만난 경우
					break;
				else if(map[nry][nrx] == '#' || (nry == nby && nrx == nbx)) {	//	벽이나 파란 구슬에 가로막힌 경우
					nry -= dy[dir];
					nrx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				
				nry += dy[dir];
				nrx += dx[dir];	//	다음 칸으로 이동
			}
		}
		else {	//	빨간 구슬 먼저 이동해도 될 경우
			//	빨간 구슬 이동시키기
			while(true) {
				if(map[nry][nrx] == 'O')	//	빨간 구슬이 구멍을 만난 경우
					break;
				else if(map[nry][nrx] == '#') {	//	벽에 가로막힌 경우
					nry -= dy[dir];
					nrx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				
				nry += dy[dir];
				nrx += dx[dir];	//	다음 칸으로 이동
			}
			
			//	파란 구슬 이동시키기
			while(true) {
				if(map[nby][nbx] == 'O')	//	파란 구슬이 구멍을 만난 경우
					break;
				if(map[nby][nbx] == '#' || (nby == nry && nbx == nrx)) {	//	벽이나 빨간 구슬에 가로막힌 경우
					nby -= dy[dir];
					nbx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
			
				nby += dy[dir];
				nbx += dx[dir];	//	다음 칸으로 이동
			}
		}
		
		res[0] = nry;
		res[1] = nrx;
		res[2] = nby;
		res[3] = nbx;
		
		return res;
	}
	
	//	현재 빨간 구슬 위치가 (ry, rx), 파란 구슬 위치가 (by, bx), 기울이는 방향이 dir일때
	//	파란 구슬이 방향에서 앞서면 < 0, 빨간 구슬이 앞서면 > 0, 상관없으면 0
	private static int compare(int ry, int rx, int by, int bx, int dir) {
		if(dir == 0)
			return rx - bx;
		else if(dir == 2)
			return bx - rx;
		else if(dir == 1)
			return ry - by;
		else
			return by - ry;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
}	//	Main-class-end
```

**Bruteforcing**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br =  new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static char[][] map;
	
	private static final int[] DIRS = new int[10];	//	10번 기울일 방향 배열
	
	private static int ry = 0;
	private static int rx = 0;	//	최초 빨간 구슬 위치
	private static int by = 0;
	private static int bx = 0;	//	최초 파란 구슬 위치
	private static int hy = 0;
	private static int hx = 0;	//	구멍 위치
	
	private static int answer = 11;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < M; x++) {
				if(map[y][x] == 'R') {
					ry = y;
					rx = x;
				}
				else if(map[y][x] == 'B') {
					by = y;
					bx = x;
				}
				if(map[y][x] == 'O') {
					hy = y;
					hx = x;
				}
			}
		}
		
		permutation(0);
		
		System.out.println(answer == 11 ? -1 : answer);
	}	//	main-end
	
	private static int[] move(int ry, int rx, int by, int bx, int dir) {
		int[] res = new int[4];
		int compare = compare(ry, rx, by, bx, dir);
		
		int nry = ry;
		int nrx = rx;	//	빨간 구슬이 최종적으로 이동할 위치
		int nby = by;
		int nbx = bx;	//	파란 구슬이 최종적으로 이동할 위치
		
		if(compare < 0) {	//	파란 구슬 먼저 이동할 경우
			//	파란 구슬 먼저 위치 정하기
			while(true) {
				if(map[nby][nbx] == '#') {	//	벽에 가로막힌 경우
					nby -= dy[dir];
					nbx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				else if(map[nby][nbx] == 'O')	//	파란 구슬이 구멍을 만난 경우
					return new int[] {nry, nrx, nby, nbx};	//	어차피 파란 구슬이 구멍에 빠진 순간 빨간 구슬은 의미 없음
			
				nby += dy[dir];
				nbx += dx[dir];	//	다음 칸으로 이동
			}
			
			//	빨간 구슬 이동시키기
			while(true) {
				if(map[nry][nrx] == 'O')	//	빨간 구슬이 구멍을 만난 경우
					break;
				else if(map[nry][nrx] == '#' || (nry == nby && nrx == nbx)) {	//	벽이나 파란 구슬에 가로막힌 경우
					nry -= dy[dir];
					nrx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				
				nry += dy[dir];
				nrx += dx[dir];	//	다음 칸으로 이동
			}
		}
		else {	//	빨간 구슬 먼저 이동해도 될 경우
			//	빨간 구슬 이동시키기
			while(true) {
				if(map[nry][nrx] == 'O')	//	빨간 구슬이 구멍을 만난 경우
					break;
				else if(map[nry][nrx] == '#') {	//	벽에 가로막힌 경우
					nry -= dy[dir];
					nrx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				
				nry += dy[dir];
				nrx += dx[dir];	//	다음 칸으로 이동
			}
			
			//	파란 구슬 이동시키기
			while(true) {
				if(map[nby][nbx] == 'O')	//	파란 구슬이 구멍을 만난 경우
					break;
				if(map[nby][nbx] == '#' || (nby == nry && nbx == nrx)) {	//	벽이나 빨간 구슬에 가로막힌 경우
					nby -= dy[dir];
					nbx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
			
				nby += dy[dir];
				nbx += dx[dir];	//	다음 칸으로 이동
			}
		}
		
		res[0] = nry;
		res[1] = nrx;
		res[2] = nby;
		res[3] = nbx;
		
		return res;
	}
	
	//	현재 빨간 구슬 위치가 (ry, rx), 파란 구슬 위치가 (by, bx), 기울이는 방향이 dir일때
	//	파란 구슬이 방향에서 앞서면 < 0, 빨간 구슬이 앞서면 > 0, 상관없으면 0
	private static int compare(int ry, int rx, int by, int bx, int dir) {
		if(dir == 0)
			return rx - bx;
		else if(dir == 2)
			return bx - rx;
		else if(dir == 1)
			return ry - by;
		else
			return by - ry;
	}
	
	private static void move() {
		int cry = ry;
		int crx = rx;	//	현재 빨간 구슬 위치
		int cby = by;
		int cbx = bx;	//	현재 파란 구슬 위치
		
		for(int i = 0; i < 10; i++) {
			int dir = DIRS[i];
			
			int[] next = move(cry, crx, cby, cbx, dir);
			int nry = next[0];
			int nrx = next[1];	//	다음 빨간 구슬 위치
			int nby = next[2];
			int nbx = next[3];	//	다음 파란 구슬 위치
			
			if(nby == hy && nbx == hx)	//	이 방법대로 이동하다보면 파란 구슬이 구멍에 빠지게 될 경우, 유효하지 않음
				return;
			
			if(nry == hy && nrx == hx) {	//	파란 구슬이 구멍에 빠지지 않으면서 빨간 구슬이 결국 구멍에 빠지면
				answer = Math.min(answer, i + 1);	//	i + 1번 기울이면 빨간 구슬을 구멍에 넣을 수 있음
				return;
			}
			
			cry = nry;
			crx = nrx;
			cby = nby;
			cbx = nbx;
		}
	}
	
	private static void permutation(int nth) {
		if(nth == 10) {	//	방법 다 정한 경우
			move();	//	정해진 방법대로 움직여보기
			return;
		}
		
		for(int dir = 0; dir < 4; dir++) {
			if(nth != 0) {
				if(DIRS[nth - 1] == dir)	//	같은 방향 연속 2번 기울이기 방지
					continue;
			}
			
			DIRS[nth] = dir;
			permutation(nth + 1);
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
}	//	Main-class-end
```

**Backtracking**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br =  new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static char[][] map;
	
	private static int ry = 0;
	private static int rx = 0;	//	최초 빨간 구슬 위치
	private static int by = 0;
	private static int bx = 0;	//	최초 파란 구슬 위치
	private static int hy = 0;
	private static int hx = 0;	//	구멍 위치
	
	private static int answer = 11;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < M; x++) {
				if(map[y][x] == 'R') {
					ry = y;
					rx = x;
				}
				else if(map[y][x] == 'B') {
					by = y;
					bx = x;
				}
				if(map[y][x] == 'O') {
					hy = y;
					hx = x;
				}
			}
		}

		permutation(0, ry, rx, by, bx, -1);
		System.out.println(answer == 11 ? -1 : answer);
	}	//	main-end
	
	private static int[] move(int ry, int rx, int by, int bx, int dir) {
		int[] res = new int[4];
		int compare = compare(ry, rx, by, bx, dir);
		
		int nry = ry;
		int nrx = rx;	//	빨간 구슬이 최종적으로 이동할 위치
		int nby = by;
		int nbx = bx;	//	파란 구슬이 최종적으로 이동할 위치
		
		if(compare < 0) {	//	파란 구슬 먼저 이동할 경우
			//	파란 구슬 먼저 위치 정하기
			while(true) {
				if(map[nby][nbx] == '#') {	//	벽에 가로막힌 경우
					nby -= dy[dir];
					nbx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				else if(map[nby][nbx] == 'O')	//	파란 구슬이 구멍을 만난 경우
					return new int[] {nry, nrx, nby, nbx};	//	어차피 파란 구슬이 구멍에 빠진 순간 빨간 구슬은 의미 없음
			
				nby += dy[dir];
				nbx += dx[dir];	//	다음 칸으로 이동
			}
			
			//	빨간 구슬 이동시키기
			while(true) {
				if(map[nry][nrx] == 'O')	//	빨간 구슬이 구멍을 만난 경우
					break;
				else if(map[nry][nrx] == '#' || (nry == nby && nrx == nbx)) {	//	벽이나 파란 구슬에 가로막힌 경우
					nry -= dy[dir];
					nrx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				
				nry += dy[dir];
				nrx += dx[dir];	//	다음 칸으로 이동
			}
		}
		else {	//	빨간 구슬 먼저 이동해도 될 경우
			//	빨간 구슬 이동시키기
			while(true) {
				if(map[nry][nrx] == 'O')	//	빨간 구슬이 구멍을 만난 경우
					break;
				else if(map[nry][nrx] == '#') {	//	벽에 가로막힌 경우
					nry -= dy[dir];
					nrx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
				
				nry += dy[dir];
				nrx += dx[dir];	//	다음 칸으로 이동
			}
			
			//	파란 구슬 이동시키기
			while(true) {
				if(map[nby][nbx] == 'O')	//	파란 구슬이 구멍을 만난 경우
					break;
				if(map[nby][nbx] == '#' || (nby == nry && nbx == nrx)) {	//	벽이나 빨간 구슬에 가로막힌 경우
					nby -= dy[dir];
					nbx -= dx[dir];	//	이전칸으로 되돌리기
					break;
				}
			
				nby += dy[dir];
				nbx += dx[dir];	//	다음 칸으로 이동
			}
		}
		
		res[0] = nry;
		res[1] = nrx;
		res[2] = nby;
		res[3] = nbx;
		
		return res;
	}
	
	//	현재 빨간 구슬 위치가 (ry, rx), 파란 구슬 위치가 (by, bx), 기울이는 방향이 dir일때
	//	파란 구슬이 방향에서 앞서면 < 0, 빨간 구슬이 앞서면 > 0, 상관없으면 0
	private static int compare(int ry, int rx, int by, int bx, int dir) {
		if(dir == 0)
			return rx - bx;
		else if(dir == 2)
			return bx - rx;
		else if(dir == 1)
			return ry - by;
		else
			return by - ry;
	}

	//	지금까지 nth번 기울여서 빨간 구슬은 (cry, crx), 파란 구슬은 (cby, cbx)에 있고, 마지막 기울인 방향이 cdir임
	private static void permutation(int nth, int cry, int crx, int cby, int cbx, int cdir) {
		for(int ndir = 0; ndir < 4; ndir++) {
			if(ndir == cdir)	//	같은 방향 연속 두번 기울일 필요 없음
				continue;
			
			int[] next = move(cry, crx, cby, cbx, ndir);
			int nry = next[0];
			int nrx = next[1];	//	nth + 1번 기울였을때 빨간 구슬 위치
			int nby = next[2];
			int nbx = next[3];	//	nth + 1번 기울였을때 파란 구슬 위치
			
			if(nby == hy && nbx == hx)	//	파란 구슬이 구멍에 빠지게 될 경우
				continue;
			
			if(nry == hy && nrx == hx) {
				answer = Math.min(answer, nth + 1);	//	nth + 1번 기울이면 빨간 구슬이 구멍에 빠짐
				return;
			}
			
			if(nth + 1 < 10)
				permutation(nth + 1, nry, nrx, nby, nbx, ndir);
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
}	//	Main-class-end
```

### **BOJ 10776 제국**

[10776번: 제국](https://boj.ma/10776/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 10_000 * 100_000 + 1;
	
	private static final BufferedReader br =  new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int K;
	private static int N;
	private static int M;
	private static int A;
	private static int B;

	private static List<int[]>[] graph;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		K = Integer.parseInt(st.nextToken());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int t = Integer.parseInt(st.nextToken());
			int h = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, t, h});
			graph[v2].add(new int[] {v1, t, h});
		}
		
		st = new StringTokenizer(br.readLine());
		A = Integer.parseInt(st.nextToken());
		B = Integer.parseInt(st.nextToken());
		
		System.out.println(dijkstra());
	}	//	main-end
	
	private static int dijkstra() {
		PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
		int[][] dist = new int[K + 1][N + 1];
		int res = INF;
		
		for(int k = 0; k <= K; k++)
			Arrays.fill(dist[k], INF);
		
		dist[K][A] = 0;
		
		pq.offer(new int[] {A, dist[K][A], K});
		
		while(!pq.isEmpty()) {
			int[] cur = pq.poll();
			int cv = cur[0];
			int ct = cur[1];
			int ch = cur[2];
			
			if(dist[ch][cv] < ct)
				continue;
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];
				int nt = ct + edge[1];
				int nh = ch - edge[2];
			
				if(nh <= 0)
					continue;
				
				if(nt < dist[nh][nv]) {
					dist[nh][nv] = nt;
					pq.offer(new int[] {nv, dist[nh][nv], nh});
				}
			}
		}
		
		for(int h = 1; h <= K; h++)
			res = Math.min(res, dist[h][B]);
		
		return res == INF ? -1 : res;
	}
}	//	Main-class-end
```