# 24_10_20_daily_certification

# Problem Solving (Algorithm & SQL)

### **CO{)E TREE** 트리의 서브트리

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/subtree-of-tree/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int R;
	private static int Q;
	
	private static List<Integer>[] tree;
	private static int[] dp;
	private static boolean[] visited;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		tree = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			tree[v] = new ArrayList<>();
		
		dp = new int[N + 1];
		visited = new boolean[N + 1];
		
		for(int i = 0; i < N - 1; i++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			tree[v1].add(v2);
			tree[v2].add(v1);
		}
		
		dfs(R);
		
		for(int q = 0; q < Q; q++)
			sb.append(dp[Integer.parseInt(br.readLine())]).append("\n");
		
		System.out.print(sb);
	}	//	main-end

	private static int dfs(int v) {
		if(dp[v] != 0)
			return dp[v];
		
		visited[v] = true;
		dp[v] = 1;
		
		for(int next : tree[v]) {
			if(!visited[next])
				dp[v] += dfs(next);
		}
		
		return dp[v];
	}
}	//	Main-class-end
```