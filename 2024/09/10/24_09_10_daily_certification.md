# 24_09_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 7535 **A Bug’s Life**

[](https://www.acmicpc.net/problem/7535)

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
	
	private static int TC;
	private static int N;
	private static int M;
	
	private static List<Integer>[] graph;
	private static int[] sex;
	
	private static boolean ok = true;
	
	public static void main(String[] args) throws IOException {
		TC = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= TC; tc++) {
			sb.append("Scenario #").append(tc).append(":\n");
			
			st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());
			
			ok = true;
			
			graph = new ArrayList[N + 1];
			for(int i = 0; i <= N; i++)
				graph[i] = new ArrayList<>();
			
			sex = new int[N + 1];
			
			for(int e = 0; e < M; e++) {
				st = new StringTokenizer(br.readLine());
				int b1 = Integer.parseInt(st.nextToken());
				int b2 = Integer.parseInt(st.nextToken());
				
				graph[b1].add(b2);
				graph[b2].add(b1);
			}
			
			for(int b = 1; b <= N; b++) {
				if(!ok)
					break;
				
				if(sex[b] == 0)
					dfs(b, 1);
			}
			
			sb.append(ok ? "No suspicious bugs found!\n" : "Suspicious bugs found!\n");
			if(tc < TC)
				sb.append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dfs(int bug, int s) {
		if(!ok)
			return;
		
		sex[bug] = s;
		
		for(int other : graph[bug]) {
			if(sex[other] == sex[bug]) {
				ok = false;
				return;
			}
			else if(sex[other] == 0)
				dfs(other, -s);
		}
	}
}	//	Main-class-end
```