# 25_06_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 27501 RGB트리

[27501번: RGB트리](http://boj.ma/27501/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int ROOT = 1;
	private static final char[] COLORS = {'R', 'G', 'B'};
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	
	private static List<Integer>[] tree;
	
	private static boolean[] visited;
	
	private static int[][] values;	//	values[v][c] : v를 c(R = 0, G = 1, B = 2)로 칠했을때 값
	private static int[][] dp;		//	dp[v][c] : v를 루트로 하는 서브트리에서 v를 c(R = 0, G = 1, B = 2)로 칠했을때의 최댓값 
	private static char[] colors;
	
	private static int max = -1;			//	최댓값
	private static int maxRootColor = -1;	//	최댓값이 되기 위해 칠해야 하는 ROOT의 색
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		tree = new ArrayList[N + 1];
		visited = new boolean[N + 1];
		values = new int[N + 1][3];
		dp = new int[N + 1][3];
		colors = new char[N];
			
		for(int v = 0; v <= N; v++)
			tree[v] = new ArrayList<>();
		
		for(int e = 0; e < N - 1; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			tree[v1].add(v2);
			tree[v2].add(v1);
		}
	
		for(int v = 1; v <= N; v++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < 3; c++)
				values[v][c] = Integer.parseInt(st.nextToken());
		}
		
		dp(ROOT);
		
		for(int c = 0; c < 3; c++) {
			if(max < dp[ROOT][c]) {
				max = dp[ROOT][c];
				maxRootColor = c;
			}
		}
		
		sb.append(max).append("\n");
		
		Arrays.fill(visited, false);
		traceback(ROOT, maxRootColor);
		sb.append(new String(colors));
		
		System.out.println(sb);
	}	//	main-end
	
	private static void traceback(int cur, int curColor) {
		visited[cur] = true;
		colors[cur - 1] = COLORS[curColor];
		
		for(int child : tree[cur]) {
			if(visited[child])
				continue;
			
			int max = -1;				//	cur을 curColor로 칠했을 때 최대가 되기 위해 필요한 child의 값
			int maxChildColor = -1;		//	그때의 child의 색
			
			for(int childColor = 0; childColor < 3; childColor++) {
				if(childColor != curColor && dp[child][childColor] > max) {
					max = dp[child][childColor];
					maxChildColor = childColor;
				}
			}
			
			traceback(child, maxChildColor);
		}
	}
	
	private static void dp(int cur) {
		visited[cur] = true;
		for(int c = 0; c < 3; c++)
			dp[cur][c] = values[cur][c];

		for(int child : tree[cur]) {
			if(visited[child])
				continue;
			
			dp(child);
			
			for(int curColor = 0; curColor < 3; curColor++) {	//	cur을 curColor로 칠한다고 가정
				int max = -1;			//	cur을 curColor로 칠할때 child로부터 받을 수 있는 최댓값
				
				for(int childColor = 0; childColor < 3; childColor++) {
					if(childColor != curColor)		//	cur과 다른 색으로 칠해야 함
						max = Math.max(max, dp[child][childColor]);	//	cur을 curColor로 칠했을때 최댓값이 되려면 child를 childColor로 칠해야 함
				}
				
				dp[cur][curColor] += max;	//	cur을 curColor로 칠할 경우, child로부터 max를 받는게 최선
			}
		}
	}
}	//	Main-class-end
```

### BOJ 25542 약속 장소

[25542번: 약속 장소](http://boj.ma/25542/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int L;
	
	private static char[][] names;
	private static char[] answer = null;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		L = Integer.parseInt(st.nextToken());
		
		names = new char[N][];
		for(int i = 0; i < N; i++)
			names[i] = br.readLine().toCharArray();
		
		for(int wc = 0; wc < L; wc++) {
			int[] diff = new int[N];	//	diff[i] : names[0]와 names[i]를 wc번째 문자를 제외하고 비교했을때 차이
			
			boolean ok = true;
			for(int i = 1; i < N; i++) {
				for(int j = 0; j < L; j++) {
					if(j == wc)
						continue;
					
					if(names[0][j] != names[i][j]) { 
						diff[i]++;
					
						if(diff[i] > 1) {
							ok = false;
							break;
						}
					}
				}
				
				if(!ok)
					break;
			}
			
			if(ok) {
				for(char ch = 'A'; ch <= 'Z'; ch++) {
					names[0][wc] = ch;
					
					boolean find = true;
					for(int i = 1; i < N; i++) {
						int cnt = 0;
						
						for(int j = 0; j < L; j++) {
							if(names[0][j] != names[i][j]) {
								cnt++;
								if(cnt > 1) {
									find = false;
									break;
								}
							}
						}
						
						if(!find)
							break;
					}
					
					if(find) {
						System.out.println(new String(names[0]));
						return;
					}
				}
			}
		}
		
		System.out.println("CALL FRIEND");
	}	//	main-end
}	//	Main-class-end
```