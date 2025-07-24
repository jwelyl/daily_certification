# 25_07_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10725 빛의 왕과 거울의 미로 1

[10725번: 빛의 왕과 거울의 미로 1](http://boj.ma/10725/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MOD = 10_007;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int X;
	private static int Y;
	
	private static char[][] board;
	
	private static final List<int[]> dcList = new ArrayList<>();
	
	private static int[] s;
	private static int sy;
	private static int sx;
	private static int sd;
	private static int[] e;
	private static int ey;
	private static int ex;
	private static int ed;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		X = Integer.parseInt(st.nextToken());
		Y = Integer.parseInt(st.nextToken());
		
		s = getPosDir(X, true);
		e = getPosDir(Y, false);
		sd = s[2];
		sy = s[0];
		sx = s[1];
		
		ed = e[2];
		ey = e[0] + dy[ed];
		ex = e[1] + dx[ed];
		
		board = new char[N][M];
		for(int y = 0; y < N; y++) {
			board[y] = br.readLine().toCharArray();
			for(int x = 0; x < M; x++) {
				if(board[y][x] == '?')
					dcList.add(new int[] {y, x});
			}
		}
		
		backtracking(0);
		
		System.out.println(answer % MOD);
	} // main-end
	
	private static void backtracking(int nth) {
		if(nth == dcList.size()) {	//	모든 ?를 다 정했을 경우
			if(traversal(sy, sx, sd))
				answer++;
			
			return;
		}
		
		int[] pos = dcList.get(nth);
		int y = pos[0];
		int x = pos[1];
		
		board[y][x] = '/';
		backtracking(nth + 1);
		board[y][x] = '\\';
		backtracking(nth + 1);
		board[y][x] = '.';
		backtracking(nth + 1);
	}
	
	private static boolean traversal(int y, int x, int d) {
		boolean[][][] visited = new boolean[N][M][4];
		visited[y][x][d] = true;
		
		int cy = y;
		int cx = x;
		int cd = d;
		
		while(true) {
			int ny = cy;
			int nx = cx;
			int nd = cd;
			
			if(board[cy][cx] == '/') {
				if(cd == 0)
					nd = 3;
				else if(cd == 1)
					nd = 2;
				else if(cd == 2)
					nd = 1;
				else
					nd = 0;
			}
			else if(board[cy][cx] == '\\') {
				if(cd == 0)
					nd = 1;
				else if(cd == 1)
					nd = 0;
				else if(cd == 2)
					nd = 3;
				else
					nd = 2;
			}
			else
				nd = cd;
		
			ny = cy + dy[nd];
			nx = cx + dx[nd];
			
			if(isIn(ny, nx)) {
				if(visited[ny][nx][nd])
					break;
				
				visited[ny][nx][nd] = true;
				cy = ny;
				cx = nx;
				cd = nd;
			}
			else {	//	격자 밖으로 벗어날 경우
				if(ny == ey && nx == ex)
					return true;
				break;
			}
		}
		
		return false;
	}
	
	private static int[] getPosDir(int num, boolean start) {
		int[] res = new int[3];
		
		if(1 <= num && num <= M) {
			res[0] = 0;
			res[1] = num - 1;
			res[2] = start ? 1 : 3;
		}
		else if(M + 1 <= num && num <= M + N) {
			res[0] = num - M - 1;
			res[1] = 0;
			res[2] = start ? 0 : 2;
		}
		else if(M + N + 1 <= num && num <= M + 2 * N) {
			res[0] = num - M - N - 1;
			res[1] = M - 1;
			res[2] = start ? 2 : 0;
		}
		else {
			res[0] = N - 1;
			res[1] = num - M - 2 * N - 1;
			res[2] = start ? 3 : 1;
		}
		
		return res;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
} // Main-class-end

```