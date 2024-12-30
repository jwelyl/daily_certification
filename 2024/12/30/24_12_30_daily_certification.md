# 24_12_30_daily_certification

### **BOJ 9885 Claws**

[9885번: Claws](https://boj.ma/9885/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;

	private static List<Edge>[] tree;
	private static int[] dist;
	private static int[] subtrees;
	private static int[] grades;
	
	private static int root;
	private static boolean[] roots;
	
	private static int ans = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		grades = new int[N + 1];
		tree = new ArrayList[N + 1];
		dist = new int[N + 1];
		subtrees = new int[N + 1];
		roots = new boolean[N + 1];
		Arrays.fill(roots, true);
		
		for(int v = 1; v <= N; v++)
			tree[v] = new ArrayList<>();
		
		for(int m = 0; m < N - 1; m++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int cost = Integer.parseInt(st.nextToken());
			
			tree[v2].add(new Edge(v1, cost));
			roots[v1] = false;
		}
		
		for(int v = 1; v <= N; v++) {
			if(roots[v]) {
				root = v;
				break;
			}
		}
		
		dfs(root, 0);
		dfs(root);

		for(int v = 1; v <= N; v++)
			grades[v] = dist[v] + subtrees[v];
		
		dfs2(root, 0);
		
		System.out.println(ans);
	}	//	main-end
	
	private static void dfs2(int v, int sum) {	
		sum += grades[v];
		
		ans = Math.max(ans, sum);
		
		for(Edge edge : tree[v])
			dfs2(edge.vertex, sum);
	}
	
	//	root로부터 각 정점까지 거리 구하기
	private static void dfs(int v, int d) {
		dist[v] = d;
		
		for(Edge edge : tree[v]) {
			int next = edge.vertex;
			int nd = d + edge.cost;
			
			dfs(next, nd);
		}
	}
	
	private static void dfs(int v) {
		for(Edge edge : tree[v]) {
			dfs(edge.vertex);
			subtrees[v] += subtrees[edge.vertex];
			subtrees[v] += edge.cost;
		}
	}
	
	private static class Edge {
		public int vertex;
		public int cost;
		
		public Edge(int vertex, int cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
	}
}	//	Main-class-end
```