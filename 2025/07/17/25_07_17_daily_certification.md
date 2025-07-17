# 25_07_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17370 **육각형 우리 속의 개미**

[17370번: 육각형 우리 속의 개미](http://boj.ma/17370/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MAX = 46;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static final boolean[][] visited = new boolean[MAX + 1][MAX + 1];
	
	private static int N;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		visited[MAX / 2][MAX / 2] = true;
		backtracking(0, MAX / 2 - 1, MAX / 2, 0);
		
		System.out.println(answer);
	}	//	main-end
	
	private static void backtracking(int nth, int y, int x, int dir) {
		visited[y][x] = true;
		
		for(int nd : nextDir[dir]) {	//	다음에 이동할 방향
			int ny = y + dy[nd];
			int nx = x + dx[nd];
			
			if(nth + 1 == N) {
				if(visited[ny][nx])
					answer++;
				continue;
			}
				
			if(!visited[ny][nx])
				backtracking(nth + 1, ny, nx, nd);
		}
		
		visited[y][x] = false;
	}

	private static final int[][] nextDir = {{1, 5}, {0, 2}, {1, 3}, {2, 4}, {3, 5}, {0, 4}};
	private static final int[] dy = {-1, -1, 1, 1,  1, -1};
	private static final int[] dx = { 0,  1, 1, 0, -1, -1};
}	//	Main-class-end
```