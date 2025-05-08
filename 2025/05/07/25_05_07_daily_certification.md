# 25_05_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2350 대운하

[2350번: 대운하](http://boj.ma/2350/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K;
	
	private static final List<int[]> edges = new ArrayList<>();
	private static int[] parents;
	
	private static List<int[]>[] tree;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		tree = new ArrayList[N + 1];
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			tree[v] = new ArrayList<>();
		}
	
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int w = Integer.parseInt(st.nextToken());
			
			edges.add(new int[] {v1, v2, w});
		}
		
		kruskal();
		
		for(int q = 0; q < K; q++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			sb.append(bfs(v1, v2)).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end

	private static int bfs(int from, int to) {
		Queue<int[]> queue = new LinkedList<>();
		boolean[] visited = new boolean[N + 1];
		
		visited[from] = true;
		queue.offer(new int[] {from, Integer.MAX_VALUE});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cv = cur[0];
			int cc = cur[1];
			
			for(int[] edge : tree[cv]) {
				int nv = edge[0];
				int nc = Math.min(cc, edge[1]);
				
				if(nv == to)
					return nc;
				
				if(!visited[nv]) {
					visited[nv] = true;
					queue.offer(new int[] {nv, nc});
				}
			}
		}
		
		return Integer.MAX_VALUE;
	}

	
	private static void kruskal() {
		int edgeCnt = 0;
		Collections.sort(edges, (e1, e2) -> Integer.compare(e2[2], e1[2]));
	
		for(int[] edge : edges) {
			int v1 = edge[0];
			int v2 = edge[1];
			int w = edge[2];
			
			if(find(v1) != find(v2)) {
				tree[v1].add(new int[] {v2, w});
				tree[v2].add(new int[] {v1, w});
				union(v1, v2);
				edgeCnt++;
				
				if(edgeCnt == N - 1)
					break;
			}
		}
	}
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2)
			parents[v2] = v1;
	}
} //	Main-class-end
```