# 24_08_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2194 유닛 이동시키기

[](https://www.acmicpc.net/problem/2194)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int OBSTACLE = -1;	//	장애물
	private static final int VISITED = 1;	//	방문 처리
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int A;
	private static int B;
	private static int K;
	
	private static int[][] board;
	
	private static int suly = 0;
	private static int sulx = 0;
	private static int euly = 0;
	private static int eulx = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		A = Integer.parseInt(st.nextToken());
		B = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		board = new int[N][M];
		
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			
			board[y][x] = OBSTACLE;
		}
		
		st = new StringTokenizer(br.readLine());
		suly = Integer.parseInt(st.nextToken()) - 1;
		sulx = Integer.parseInt(st.nextToken()) - 1;
		st = new StringTokenizer(br.readLine());
		euly = Integer.parseInt(st.nextToken()) - 1;
		eulx = Integer.parseInt(st.nextToken()) - 1;
		
		System.out.println(bfs());
	}	//	main-end
	
	private static int bfs() {
		Queue<Point> q = new LinkedList<>();
		int ans = 0;
		
		board[suly][sulx] = VISITED;
		q.offer(new Point(suly, sulx));
		
		while(!q.isEmpty()) {
			int size = q.size();
			
			for(int s = 0; s < size; s++) {
				Point cur = q.poll();
				int culy = cur.y;
				int culx = cur.x;

				for(int d = 0; d < 4; d++) {
					if(canMove(culy, culx, d)) {	//	방문 가능할 경우					
						if(culy + dy[d] == euly && culx + dx[d] == eulx)	//	도착 지점에 도달한 경우
							return ans + 1;
						
						board[culy + dy[d]][culx + dx[d]] = VISITED;
						q.offer(new Point(culy + dy[d], culx + dx[d]));
					}
				}
			}
			
			ans++;
		}	//	while-end
		
		return -1;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean canMove(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M) && board[y][x] != OBSTACLE;
	}
	
	//	dir 방향으로 유닛 이동 가능하면 true, 아니면 false
	private static boolean canMove(int culy, int culx, int dir) {
		int nuly = culy + dy[dir];
		int nulx = culx + dx[dir];
		
		if(!canMove(nuly, nulx) || board[nuly][nulx] == VISITED)
			return false;
        
		for(int y = culy; y < culy + A; y++) {
			for(int x = culx; x < culx + B; x++) {
				int ny = y + dy[dir];
				int nx = x + dx[dir];
				
				if(!canMove(ny, nx))
					return false;
			}
		}
		
		return true;
	}
	
	private static class Point {
		public int y;
		public int x;
		
		public Point(int y, int x) {
			this.y = y;
			this.x = x;
		}
	}
}	//	Main-class-end
```