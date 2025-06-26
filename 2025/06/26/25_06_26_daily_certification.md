# 25_06_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1623 신년 파티

[1623번: 신년 파티](http://boj.ma/1623/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int ROOT = 1;
	private static final int NONE = Integer.MIN_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	
	private static List<Integer>[] tree;
	private static int[] values;
	
	private static int[][] dp;		//	dp[v][0/1] : v를 루트로 하는 서브트리가 있을때, v를 포함 X/O했을때 최댓값
	
	private static final List<Integer> list0 = new ArrayList<>();
	private static final List<Integer> list1 = new ArrayList<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		tree = new ArrayList[N + 1];
		values = new int[N + 1];
		dp = new int[N + 1][2];
			
		for(int v = 0; v <= N; v++) {
			tree[v] = new ArrayList<>();
			dp[v][0] = NONE;
			dp[v][1] = NONE;
		}
		
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++)
			values[v] = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		for(int v = 2; v <= N; v++)
			tree[Integer.parseInt(st.nextToken())].add(v);
		
		dp(ROOT);
		
		traceback(ROOT, 0, list0);
		traceback(ROOT, 1, list1);
		Collections.sort(list0);
		Collections.sort(list1);
		
		sb.append(dp[ROOT][1]).append(" ").append(dp[ROOT][0]).append("\n");
		for(int num : list1)
			sb.append(num).append(" ");
		sb.append("-1\n");
		for(int num : list0)
			sb.append(num).append(" ");
		sb.append("-1\n");
		
		System.out.print(sb);
	}	//	main-end
	
	private static void traceback(int cur, int selected, List<Integer> list) {
		if(selected == 1)
			list.add(cur);
		
		for(int child : tree[cur]) {
			if(selected == 1)	//	cur을 포함한 경우
				traceback(child, 0, list);	//	child는 포함될 수 없음
			else {	//	cur을 포함하지 않은 경우
				if(dp[child][0] > dp[child][1])	//	child를 포함하지 않는게 이득인 경우
					traceback(child, 0, list);
				else							//	child를 포함하는게 이득인 경우
					traceback(child, 1, list);
			}
		}
	}
	
	private static void dp(int cur) {
		dp[cur][0] = 0;
		dp[cur][1] = values[cur];
		
		for(int child : tree[cur]) {
			dp(child);
			
			dp[cur][1] += dp[child][0];
			dp[cur][0] += Math.max(dp[child][1], dp[child][0]);
		}
	}
}	//	Main-class-end
```