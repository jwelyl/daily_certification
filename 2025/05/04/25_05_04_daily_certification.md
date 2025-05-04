# 25_05_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16957 체스판 위의 공

[16957번: 체스판 위의 공](http://boj.ma/16957/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int R; 
	private static int C;

	private static int[][] board;
	private static int[] parents;
	private static int[] dsSizes;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		
		board = new int[R][C];
		parents = new int[R * C];
		dsSizes = new int[R * C];
		
		for(int v = 0; v < R * C; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		for(int r = 0; r < R; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < C; c++)
				board[r][c] = Integer.parseInt(st.nextToken());
		}
		
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int cv = C * r + c;
				int minNum = board[r][c];
				int minR = -1;
				int minC = -1;
				
				for(int d = 0; d < 8; d++) {
					int nr = r + dy[d];
					int nc = c + dx[d];
					
					if(isIn(nr, nc)) {
						if(board[nr][nc] < minNum) {
							minNum = board[nr][nc];
							minR = nr;
							minC = nc;
						}
					}
				}
				
				if(minR != -1 && minC != -1) {
					int nv = C * minR + minC;
					union(cv, nv);
				}
			}
		}
		
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int v = C * r + c;
				int ds = find(v);
				
				sb.append(v == ds ? dsSizes[ds] : 0).append(" ");
			}
			sb.append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			parents[v1] = v2;
			dsSizes[v2] += dsSizes[v1];
			dsSizes[v1] = 0;
		}
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < R) && (0 <= x && x < C);
	}
}	//	Main-class-end
```