# 25_05_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20166 문자열 지옥에 빠진 호석

[20166번: 문자열 지옥에 빠진 호석](http://boj.ma/20166/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_D = 5;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K;
	
	private static char[][] board;
	
	private static final Map<String, Integer> map = new HashMap<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		board = new char[N][];
		for(int y = 0; y < N; y++)
			board[y] = br.readLine().toCharArray();
	
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++)
				dfs(y, x, 0, new char[MAX_D]);
		}
	
		for(int q = 0; q < K; q++)
			sb.append(map.getOrDefault(br.readLine(), 0)).append("\n");
	
		System.out.print(sb);
	}	//	main-end
	
	private static void dfs(int y, int x, int depth, char[] strArr) {
		if(depth == MAX_D)
			return;
		
		strArr[depth] = board[y][x];
		StringBuilder sb = new StringBuilder();
		for(int i = 0; i <= depth; i++)
			sb.append(strArr[i]);
		String str = sb.toString();
		
		map.put(str, map.getOrDefault(str, 0) + 1);
		
		for(int d = 0; d < 8; d++) {
			int ny = (y + dy[d] + N) % N;
			int nx = (x + dx[d] + M) % M;
			
			dfs(ny, nx, depth + 1, strArr);
		}
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};	
}	//	Main-class-end
```

### BOJ 5588 별자리 찾기

[5588번: 별자리 찾기](http://boj.ma/5588/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Objects;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int M;	//	별자리에 포함된 별 개수
	private static int N;	//	지도에 포함된 별 개수
	
	private static Star[] stars;
	private static int[][] diffs;
	
	private static final Set<Star> starSet = new HashSet<>();
	
	public static void main(String[] args) throws IOException {
		M = Integer.parseInt(br.readLine());
		
		stars = new Star[M];
		diffs = new int[M][2];
		for(int i = 0; i < M; i++) {
			st = new StringTokenizer(br.readLine());
			stars[i] = new Star(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
		
			//	stars[0]를 기준 별로 다른 별들과의 좌표 차이 계산
			if(i > 0) {
				diffs[i][0] = stars[i].y - stars[0].y;
				diffs[i][1] = stars[i].x - stars[0].x;
			}
		}
		
		N = Integer.parseInt(br.readLine());
		for(int s = 0; s < N; s++) {
			st = new StringTokenizer(br.readLine());
			starSet.add(new Star(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
		}
		
		//	star를 기준별로 생각
		for(Star star : starSet) {
			boolean find = true;
			int sy = star.y;
			int sx = star.x;
			
			for(int i = 1; i < M; i++) {
				Star target = new Star(sy + diffs[i][0], sx + diffs[i][1]);
				
				if(!starSet.contains(target)) {
					find = false;
					break;
				}
			}
			
			if(find) {	//	찾음
				System.out.println(sy - stars[0].y +  " " + (sx - stars[0].x));
				break;
			}
		}
	}	//	main-end
	
	private static class Star {
		public int y;
		public int x;
		
		public Star(int y, int x) {
			this.y = y;
			this.x = x;
		}
		
		@Override
		public boolean equals(Object obj) {
			if(obj == this)
				return true;
			
			if(!(obj instanceof Star))
				return false;
			
			Star star = (Star)obj;
			return star.y == this.y && star.x == this.x;
		}
		
		@Override
		public int hashCode() {
			return Objects.hash(this.y, this.x);
		}
	}
}	//	Main-class-end
```