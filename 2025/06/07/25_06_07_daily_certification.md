# 25_06_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25685 좋은 노드 집합 찾기

[25685번: 좋은 노드 집합 찾기](http://boj.ma/25685/t)

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
	
	private static int T;	//	테스트케이스 개수
	private static int N;	//	정점 개수
	
	private static int root;
	private static int[] values;	//	values[v] : v의 값
	
	private static List<Integer>[] tree;
	
	private static long[][] dp;		//	dp[v][0/1] : v를 루트로 하는 서브트리에서 v를 선택하지 않았을 때/선택했을 때 최댓값 

	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			N = Integer.parseInt(br.readLine());
			values = new int[N + 1];
			
			tree = new ArrayList[N + 1];
			dp = new long[N + 1][2];
			
			for(int v = 0; v <= N; v++)
				tree[v] = new ArrayList<>();
			
			st = new StringTokenizer(br.readLine());
			for(int v = 1; v <= N; v++)
				values[v] = Integer.parseInt(st.nextToken());
			st = new StringTokenizer(br.readLine());
			for(int v = 1; v <= N; v++) {
				int p = Integer.parseInt(st.nextToken());
				if(p == 0)
					root = v;
				else
					tree[p].add(v);
			}
			
			dp(root);
			sb.append(Math.max(dp[root][0], dp[root][1])).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dp(int cur) {
		dp[cur][1] = values[cur];	//	cur를 포함할 경우, 일단 cur 정점 값 확보
		dp[cur][0] = 0;
		
		if(tree[cur].size() == 0)	//	cur이 leaf node일 경우
			return;
		
		long minus = Long.MAX_VALUE;	//	cur을 포함하지 않을 경우, 자식 노드를 적어도 하나는 선택해서 발생하는 손해
		
		for(int child : tree[cur]) {
			dp(child);
			dp[cur][1] += dp[child][0];	//	cur은 포함할 경우, cur의 자식들은 모두 포함하지 않음
			dp[cur][0] += Math.max(dp[child][0], dp[child][1]);	//	일단 자식 중 하나는 반드시 선택해야 한다는 조건 없이 최댓값 선택
		
			//	자식을 적어도 하나 포함해야 한다는 조건이 없다면, 최적은 dp[child][0], dp[child][1] 중 더 큰 값을 더하는 것임
			//	이때 child를 반드시 선택했을때 발생하는 손해
			long decrease = Math.max(dp[child][0], dp[child][1]) - dp[child][1];
			
			minus = Math.min(minus, decrease);	//	손해를 최소화하기
		}
		
		dp[cur][0] -= minus;	//	손해 반영
	}
}	//	Main-class-end
```