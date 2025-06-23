# 25_06_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 30242 N-Queen (Easy)

[30242번: 🧩 N-Queen (Easy)](http://boj.ma/30242/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	
	private static int[] xpos;
	
	//	occupied[y][x] = true : (y, x)칸은 먼저 주어진 퀸의 공격 범위이므로 고려할 필요 없음
	private static boolean[][] occupied;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		xpos = new int[N + 1];
		occupied = new boolean[N + 1][N + 1];
		
		st = new StringTokenizer(br.readLine());
		for(int y = 1; y <= N; y++) {
			int x = Integer.parseInt(st.nextToken());
			
			if(x != 0) {
				occupied[y][x] = true;
				xpos[y] = x;	//	y행에 미리 놓인 퀸의 x좌표가 x
				
				for(int d = 0; d < 8; d++) {
					for(int k = 1; ; k++) {
						int ny = y + dy[d] * k;
						int nx = x + dx[d] * k;
						
						if(!isIn(ny, nx))
							break;
							
						occupied[ny][nx] = true;
					}
				}
			}
		}
		
		backtracking(1);
		
		System.out.println(-1);
	}	// main-end
	
	private static void backtracking(int nth) {
		if(nth == N + 1) {	//	N개 행에 퀸을 모두 놓은 경우
			for(int y = 1; y <= N; y++)
				sb.append(xpos[y]).append(" ");
			System.out.println(sb);
			
			System.exit(0);
		}
		
		if(xpos[nth] != 0)	//	이미 nth번째 행에는 퀸이 정해졌을 경우
			backtracking(nth + 1);
		
		for(int x = 1; x <= N; x++) {
			if(occupied[nth][x])	//	(nth, x)칸은 고려할 필요가 없음
				continue;
			
			if(canPut(nth, x)) {	//	(nth, x)칸에 놓아도 괜찮을 경우
				xpos[nth] = x;
				backtracking(nth + 1);
				xpos[nth] = 0;
			}
		}
	}
	
	private static boolean canPut(int y, int x) {
		for(int py = 1; py < y; py++) {
			int px = xpos[py];	//	이번에 (y, x)에 놓인 퀸 이전에 놓인 퀸의 좌표
			
			if(Math.abs(py - y) == Math.abs(px - x) || x == px)
				return false;
		}
		
		return true;
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= N) && (1 <= x && x <= N);
	}
}	//	Main-class-end
```