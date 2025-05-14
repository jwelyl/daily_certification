# 25_05_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15509 **Xayahh-Rakann at Moloco (Hard)**

[15509번: Xayahh-Rakann at Moloco (Hard)](http://boj.ma/15509/t)

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
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N; 
	private static int M;
	private static int K;

	private static int[] parents;
	private static int[] dsSizes;
	private static int dsCnts;	//	분리집합 개수
	
	private static final Set<Integer> dsSet = new HashSet<>();
	private static int[] dsArr;
	private static boolean[][] dp;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		dsCnts = N;
		
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			union(v1, v2);
		}
		
		dsArr = new int[dsCnts + 1];
		dp = new boolean[dsCnts + 1][K + 1];
		dp[0][0] = true;
		
		int idx = 1;
		for(int v = 1; v <= N; v++) {
			int ds = find(v);
			
			if(!dsSet.contains(ds)) {
				dsArr[idx] = ds;
				dsSet.add(ds);
				idx++;
			}
		}
		
		for(int i = 1; i <= dsCnts; i++) {
			int ds = dsArr[i];
			int dsSize = dsSizes[ds];
			
			for(int j = 0; j <= K; j++) {
				if(dsSize > j)
					dp[i][j] = dp[i - 1][j];
				else
					dp[i][j] = dp[i - 1][j] || dp[i - 1][j - dsSize];
				
				if(j == K && dp[i][j]) {
					System.out.println("SAFE");
					return;
				}
			}
		}
		
		System.out.println("DOOMED");
	}	//	main-end
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			parents[v2] = v1;
			dsSizes[v1] += dsSizes[v2];
			dsSizes[v2] = 0;
			dsCnts--;
		}
	}
}	//	Main-class-end
```