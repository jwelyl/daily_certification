# 25_07_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17831 대기업 승범이네

[17831번: 대기업 승범이네](http://boj.ma/17831/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int ROOT = 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int[] sp;
	
	//	dp[v][0] : v가 멘토로 멘토링에 참가하지 않을때 v를 루트로 하는 서브트리의 시너지 최대 합
	//	dp[v][1] : v가 멘토로 멘토링에 참가할 때 v를 루트로 하는 서브트리의 시너지 최대 합
	private static int[][] dp;
	
	private static List<Integer>[] tree;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
	
		sp = new int[N + 1];
		tree = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			tree[v] = new ArrayList<>();
		
		st = new StringTokenizer(br.readLine());
		for(int v = 2; v <= N; v++) {
			int parent = Integer.parseInt(st.nextToken());
			tree[parent].add(v);
		}
			
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++)
			sp[v] = Integer.parseInt(st.nextToken());
		
		dp = new int[N + 1][2];
		
		dfs(ROOT);
		
		System.out.println(Math.max(dp[ROOT][0], dp[ROOT][1]));
	}	//	main-end
	
	private static void dfs(int cur) {
		if(tree[cur].size() == 0) {	//	cur의 부사수가 없을 경우(leaf-node)
			dp[cur][0] = 0;
			dp[cur][1] = 0;
			return;
		}
		
		int curMentor = 0;	//	cur이 멘토로 참가하는 경우 시너지 최대 합
		for(int child : tree[cur]) {
			dfs(child);
			
			int max = Math.max(dp[child][0], dp[child][1]);
			dp[cur][0] += max;
			curMentor += max;
		}
		
		dp[cur][1] = Integer.MIN_VALUE;
		
		for(int child : tree[cur]) {	//	cur이 멘토, child가 멘티가 된다고 가정
			int res = curMentor;
			res -= Math.max(dp[child][0], dp[child][1]);
			res += sp[cur] * sp[child];
			res += dp[child][0];
			
			dp[cur][1] = Math.max(dp[cur][1], res);
		}
	}
}	//	Main-class-end
```