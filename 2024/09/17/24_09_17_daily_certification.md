# 24_09_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23034 조별과제 멈춰!

[](https://www.acmicpc.net/problem/23034)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	
	private static final List<Edge> edges = new ArrayList<>();
	private static List<int[]>[] mst;
	
	private static int mstCost = 0;
	
	private static int Q;	//	쿼리 개수
	private static int X;
	private static int Y;	//	두 팀장
	
	private static int[] prevVertices;
	private static int[] prevEdgeCost;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		mst = new ArrayList[N + 1];
		parents = new int[N + 1];
		for(int i = 0; i <= N; i++) {
			mst[i] = new ArrayList<>();
			parents[i] = i;
		}
		
		prevVertices = new int[N + 1];
		prevEdgeCost = new int[N + 1];
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			edges.add(new Edge(v1, v2, c));
		}
		
		kruskal();
		
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < Q; q++) {
			Arrays.fill(prevVertices, 0);
			Arrays.fill(prevEdgeCost, 0);
			
			st = new StringTokenizer(br.readLine());
			X = Integer.parseInt(st.nextToken());
			Y = Integer.parseInt(st.nextToken());
			
			dfs(X, Y, new boolean[N + 1]);
			
			int maxEdgeCost = 0;
			
			int cur = Y;
			
			while(cur != X) {
				int prev = prevVertices[cur];
				int cost = prevEdgeCost[cur];
				
				maxEdgeCost = Math.max(maxEdgeCost, cost);
				
				cur = prev;
			}
			
			sb.append(mstCost - maxEdgeCost).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dfs(int cur, int end, boolean[] visited) {		
		visited[cur] = true;
		
		for(int[] edge : mst[cur]) {
			int v = edge[0];
			int c = edge[1];
			
			if(!visited[v]) {
				prevVertices[v] = cur;
				prevEdgeCost[v] = c;
				
				if(v == end)
					return;
				
				dfs(v, end, visited);
			}
		}
	}
	
	private static int[] parents;
	
	private static int findDS(int v) {
		if(v == parents[v])
			return v;
		
		return parents[v] = findDS(parents[v]);
	}
	
	private static void union(int v1, int v2) {
		parents[v1] = v2;
	}
	
	private static void kruskal() {
		Collections.sort(edges);
		int edgeCnt = 0;
		
		for(Edge edge : edges) {
			int v1 = edge.v1;
			int v2 = edge.v2;
			int c = edge.cost;
			
			int v1DS = findDS(v1);
			int v2DS = findDS(v2);
			
			if(v1DS != v2DS) {
				union(v1DS, v2DS);
				edgeCnt++;
				mstCost += c;
				
				mst[v1].add(new int[] {v2, c});
				mst[v2].add(new int[] {v1, c});
			}
			
			if(edgeCnt == N - 1)
				break;
		}
	}
	
	private static class Edge implements Comparable<Edge> {
		public int v1;
		public int v2;
		public int cost;
		
		public Edge(int v1, int v2, int cost) {
			this.v1 = v1;
			this.v2 = v2;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Edge other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```