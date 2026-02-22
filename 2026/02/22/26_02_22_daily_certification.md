# 26_02_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1432 그래프 수정

[1432번: 그래프 수정](http://boj.ma/1432/t)

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
	
	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	
	private static int[][] edges;	//	간선 정보 edges[e] : {v1, v2} (e번째 간선이 연결하는 두 정점 v1, v2)
	
	private static List<Integer>[] graph;	//	graph[v] : 정점 v와 연결된 간선 번호 리스트
	private static List<Integer>[] tree;
	private static boolean[] visited;
	
	private static int dsCnts;		//	분리집합 개수
	private static int[] parents; 	//	parents[v] : v의 부모 (v가 속한 분리집합 우두머리)
	private static int[] dsSizes;	//	dsSizes[v] : v가 속한 분리집합 크기
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		if(N <= 2)	//	정점이 2개 이하이면 크기가 다른 두 트리로 나눌 수 없음
			fail();
		
		edges = new int[M + 1][2];
		
		graph = new ArrayList[N + 1];
		tree = new ArrayList[N + 1];
		visited = new boolean[N + 1];
		
		dsCnts = N;
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		
		for(int v = 0; v <= N; v++) {
			graph[v] = new ArrayList<>();
			tree[v] = new ArrayList<>();
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		for(int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			edges[e][0] = v1;
			edges[e][1] = v2;
			
			graph[v1].add(e);
			graph[v2].add(e);
			
			if(find(v1) != find(v2)) {
				union(v1, v2);
				tree[v1].add(e);
				tree[v2].add(e);
			}
		}
		
		if(dsCnts > 2)	//	이미 3개 이상의 연결요소로 이루어졌을 경우, 2개의 트리로 만들 수 없음
			fail();
		else if(dsCnts == 2) {	//	2개의 연결요소로 이루어졌을 경우
			int root1 = -1;
			int root2 = -1;	//	 2개의 분리집합의 우두머리 정점
			
			for(int v = 1; v <= N; v++) {
				if(v == parents[v]) {
					if(root1 == -1)
						root1 = v;
					else
						root2 = v;
				}
			}
			
			if(dsSizes[root1] == dsSizes[root2])	//	두 연결요소 크기가 같을 경우
				fail();
			
			sb.append(dsSizes[root1]).append(" ").append(dsSizes[root2]).append("\n");
		
			List<Integer> vList1 = new ArrayList<>();
			List<Integer> vList2 = new ArrayList<>();	//	root1, root2 연결요소에 속한 정점 리스트
			List<Integer> eList1 = new ArrayList<>();
			List<Integer> eList2 = new ArrayList<>();	//	root1, root2 연결요소로 만든 spanning tree에 속한 간선 리스트
		
			dfs(graph, root1, vList1, eList1);
			dfs(graph, root2, vList2, eList2);
			
			for(int v : vList1)
				sb.append(v).append(" ");
			sb.append("\n");
			for(int e : eList1)
				sb.append(e).append(" ");
			sb.append("\n");
			for(int v : vList2)
				sb.append(v).append(" ");
			sb.append("\n");
			for(int e : eList2)
				sb.append(e).append(" ");
			sb.append("\n");
		}
		else {	//	1개의 연결요소로 이루어진 경우, 정점이 3개 이상이므로 리프노드 중 하나만 분리하면 가능
			int leaf = -1;
			int root = 2;
			for(int v = 1; v <= N; v++) {
				if(tree[v].size() == 1) {
					leaf = v;
					break;
				}
				else root = v;
			}
			
			visited[leaf] = true;
			
			sb.append(N - 1).append(" ").append(1).append("\n");
			
			List<Integer> vList = new ArrayList<>();
			List<Integer> eList = new ArrayList<>();
			
			dfs(tree, root, vList, eList);
			for(int v : vList)
				sb.append(v).append(" ");
			sb.append("\n");
			for(int e : eList)
				sb.append(e).append(" ");
			sb.append("\n");
			sb.append(leaf).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dfs(List<Integer>[] graph, int v, List<Integer> vList, List<Integer> eList) {
		visited[v] = true;
		vList.add(v);
	
		for(int e : graph[v]) {
			int[] edge = edges[e];
			int v1 = edge[0];
			int v2 = edge[1];
			
			int next = v1 == v ? v2 : v1;
			if(!visited[next]) {
				eList.add(e);
				dfs(graph, next, vList, eList);
			}
		}
	}
	
	private static void fail() {
		System.out.println(-1);
		System.exit(0);
	}
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			parents[v2] = v1;
			dsSizes[v1] += dsSizes[v2];
			dsSizes[v2] = 0;
			dsCnts--;
		}
	}
	
}	//	Main-class-end
```