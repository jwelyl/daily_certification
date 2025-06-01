# 25_06_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15458 Barn Painting

[15458번: Barn Painting](http://boj.ma/15458/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MOD = 1_000_000_007;
	private static final int NONE = 0;
	private static final int ROOT = 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	private static int K;	//	미리 칠해진 정점 개수
	
	private static List<Integer>[] graph;
	private static List<Integer>[] tree;
	
	private static boolean[] visited;
	private static int[] colored;		//	colored[v] : v가 미리 칠해진 경우 칠해진 색, 그렇지 않을 경우 NONE
	
	private static long[][] dp;		//	dp[v][c] : v를 루트로 하는 서브트리를 c(1 <= c <= 3)로 칠하는 경우의 수 
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		tree = new ArrayList[N + 1];
		visited = new boolean[N + 1];
		colored = new int[N + 1];
		dp = new long[N + 1][4];
			
		for(int v = 0; v <= N; v++) {
			graph[v] = new ArrayList<>();
			tree[v] = new ArrayList<>();
		}
		
		for(int e = 0; e < N - 1; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			graph[v1].add(v2);
			graph[v2].add(v1);
		}
	
		dfs(ROOT);	//	ROOT가 루트인 단방향 트리 만들기
		
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			int v = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			colored[v] = c;
		}
		
		dp(ROOT);
		
		System.out.println((dp[ROOT][1] + dp[ROOT][2] + dp[ROOT][3]) % MOD);
	}	//	main-end
	
	//	cur 정점을 칠하는 경우의 수
	private static void dp(int cur) {
		if(tree[cur].size() == 0) {	//	leaf node일 경우
			int color = colored[cur];
			
			dp[cur][color] = 1;	//	cur을 color로 칠하는 경우는 1가지 뿐임
			if(color == NONE) {	//	leaf node의 색이 정해지지 않은 경우
				dp[cur][1] = 1;
				dp[cur][2] = 1;
				dp[cur][3] = 1;	//	cur을 각 색으로 칠하는 경우의 수가 1개씩 존재
			}
			
			return;
		}
		
		for(int child : tree[cur])
			dp(child);
		
		int color = colored[cur];
		if(color != NONE) {	//	cur을 칠할 색이 문제에서 주어진 경우
			long cnt = 1;	//	cur을 color로 칠하는 경우의 수
			
			for(int child : tree[cur]) {	//	cur의 자식들에 대하여
				long childCnt = 0;	//	cur을 color로 칠할때 child를 color가 아닌 색으로 칠하는 경우의 수
				
				for(int childColor = 1; childColor <= 3; childColor++) {	
					if(color != childColor)	//	child가 cur이 칠해진 color 색과 다른 색으로 칠해진 경우의 수를 더함
						childCnt += dp[child][childColor];
				}
				
				cnt = ((cnt % MOD) * (childCnt % MOD)) % MOD;
			}
			
			dp[cur][color] = cnt;
		}
		else {	//	cur을 칠할 색이 문제에서 정해지지 않은 경우
			for(int curColor = 1; curColor <= 3; curColor++) {	//	cur을 curColor로 칠한다고 가정
				long cnt = 1;	//	cur을 color로 칠하는 경우의 수	
				
				for(int child : tree[cur]) {	//	cur의 자식들에 대하여
					long childCnt = 0;	//	cur을 curColor로 칠할때 child를 curColor가 아닌 색으로 칠하는 경우의 수
					
					for(int childColor = 1; childColor <= 3; childColor++) {	
						if(curColor != childColor)	//	child가 cur이 칠해진 curColor 색과 다른 색으로 칠해진 경우의 수를 더함
							childCnt += dp[child][childColor];
					}
					
					cnt = ((cnt % MOD) * (childCnt % MOD)) % MOD;
				}
				
				dp[cur][curColor] = cnt;
			}
		}
	}
	
	private static void dfs(int cur) {
		visited[cur] = true;
	
		for(int child : graph[cur]) {
			if(!visited[child]) {
				tree[cur].add(child);
				dfs(child);
			}
		}
	}
}	//	Main-class-end
```