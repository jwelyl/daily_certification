# 24_09_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5913 준규와 사과

[](https://www.acmicpc.net/problem/5913)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int K = 0;
	
	private static final boolean[][] visited = new boolean[5][5];
	
	private static int ans = 0;
	
	public static void main(String[] args) throws IOException {
		K = Integer.parseInt(br.readLine());
		
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			visited[Integer.parseInt(st.nextToken()) - 1][Integer.parseInt(st.nextToken()) - 1] = true;
		}
		
		dfs(0, 0, 25 - K);
		
		System.out.println(ans);
	}	//	main-end
	
	private static void dfs(int y, int x, int num) {
		if(y == 4 && x == 4) {
			if(num == 1)
				ans++;
			
			return;
		}
		
		visited[y][x] = true;
		
		for(int d = 0; d < 4; d++) {
			int ny = y + dy[d];
			int nx = x + dx[d];
			
			if(isIn(ny, nx) && !visited[ny][nx])
				dfs(ny, nx, num - 1);
		}
		
		visited[y][x] = false;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < 5) && (0 <= x && x < 5);
	}
}	//	Main-class-end
```