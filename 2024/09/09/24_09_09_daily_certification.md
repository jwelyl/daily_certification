# 24_09_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12078 **gCampus (Small)**

[](https://www.acmicpc.net/problem/12078)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 100_000_001;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int T;
	
	private static int N;
	private static int M;
	
	private static List<Integer>[] graph;
	private static List<Integer>[] reversedGraph;
	private static Edge[] edges;
	private static int[] dist;
	
	private static final Set<Integer> usedEdgeSet = new HashSet<>();
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
	
		for(int tc = 1; tc <= T; tc++) {
			sb.append("Case #").append(tc).append(":\n");
			
			usedEdgeSet.clear();
			
			st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());
			
			graph = new ArrayList[N];
			reversedGraph = new ArrayList[N];
			for(int i = 0; i < N; i++) {
				graph[i] = new ArrayList<>();
				reversedGraph[i] = new ArrayList<>();
			}
			
			edges = new Edge[M];
			dist = new int[N];
			
			for(int i = 0; i < M; i++) {
				st = new StringTokenizer(br.readLine());
				int u = Integer.parseInt(st.nextToken());
				int v = Integer.parseInt(st.nextToken());
				int c = Integer.parseInt(st.nextToken());
				
				edges[i] = new Edge(i, u, v, c);
				graph[u].add(i);
				graph[v].add(i);
			}
			
			for(int start = 0; start < N; start++)
				dijkstra(start);
			
			int cnt = 0;
			for(int e = 0; e < M; e++) {
				if(!usedEdgeSet.contains(e)) {
					cnt++;
					sb.append(e).append(" ");
				}
			}
			if(cnt != 0)
				sb.append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dijkstra(int start) {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		Arrays.fill(dist, INF);
		dist[start] = 0;
		
		pq.offer(new Node(start, dist[start]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			int cc = cur.cost;
			
			if(dist[cv] < cc)
				continue;
			
			for(int edge : graph[cv]) {
				int edgeNum = edges[edge].num;
				int nv = edges[edge].vertex1 == cv ? edges[edge].vertex2 : edges[edge].vertex1;
				int nc = cc + edges[edge].cost;
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					reversedGraph[nv].clear();
					reversedGraph[nv].add(edgeNum);
					pq.offer(new Node(nv, dist[nv]));
				}
				else if(nc == dist[nv])
					reversedGraph[nv].add(edgeNum);
			}
		}
		
		for(int v = 0; v < N; v++) {
			for(int e : reversedGraph[v])
				usedEdgeSet.add(e);
		}
	}
	
	private static class Edge {
		public int num;			//	간선 번호
		public int vertex1;
		public int vertex2;		//	간선이 연결하는 두 정점
		public int cost;		//	간선 비용
		
		public Edge(int num, int vertex1, int vertex2, int cost) {
			this.num = num;
			this.vertex1 = vertex1;
			this.vertex2 = vertex2;
			this.cost = cost;
		}
	}
	
	private static class Node implements Comparable<Node> {
		public int vertex;	
		public int cost;
		
		public Node(int vertex, int cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Node other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```

### BOJ 12079 **gCampus (Large)**

[](https://www.acmicpc.net/problem/12079)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final long INF = 10_000_000_001L;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int T;
	
	private static int N;
	private static int M;
	
	private static List<Integer>[] graph;
	private static List<Integer>[] reversedGraph;
	private static Edge[] edges;
	private static long[] dist;
	
	private static final Set<Integer> usedEdgeSet = new HashSet<>();
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
	
		for(int tc = 1; tc <= T; tc++) {
			sb.append("Case #").append(tc).append(":\n");
			
			usedEdgeSet.clear();
			
			st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());
			
			graph = new ArrayList[N];
			reversedGraph = new ArrayList[N];
			for(int i = 0; i < N; i++) {
				graph[i] = new ArrayList<>();
				reversedGraph[i] = new ArrayList<>();
			}
			
			edges = new Edge[M];
			dist = new long[N];
			
			for(int i = 0; i < M; i++) {
				st = new StringTokenizer(br.readLine());
				int u = Integer.parseInt(st.nextToken());
				int v = Integer.parseInt(st.nextToken());
				int c = Integer.parseInt(st.nextToken());
				
				edges[i] = new Edge(i, u, v, c);
				graph[u].add(i);
				graph[v].add(i);
			}
			
			for(int start = 0; start < N; start++)
				dijkstra(start);
			
			for(int e = 0; e < M; e++) {
				if(!usedEdgeSet.contains(e))
					sb.append(e).append(" ");
			}
			sb.append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dijkstra(int start) {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		Arrays.fill(dist, INF);
		dist[start] = 0;
		
		pq.offer(new Node(start, dist[start]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			long cc = cur.cost;
			
			if(dist[cv] < cc)
				continue;
			
			for(int edge : graph[cv]) {
				int edgeNum = edges[edge].num;
				int nv = edges[edge].vertex1 == cv ? edges[edge].vertex2 : edges[edge].vertex1;
				long nc = cc + edges[edge].cost;
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					reversedGraph[nv].clear();
					reversedGraph[nv].add(edgeNum);
					pq.offer(new Node(nv, dist[nv]));
				}
				else if(nc == dist[nv])
					reversedGraph[nv].add(edgeNum);
			}
		}
		
		for(int v = 0; v < N; v++) {
			for(int e : reversedGraph[v])
				usedEdgeSet.add(e);
		}
	}
	
	private static class Edge {
		public int num;			//	간선 번호
		public int vertex1;
		public int vertex2;		//	간선이 연결하는 두 정점
		public long cost;		//	간선 비용
		
		public Edge(int num, int vertex1, int vertex2, int cost) {
			this.num = num;
			this.vertex1 = vertex1;
			this.vertex2 = vertex2;
			this.cost = cost;
		}
	}
	
	private static class Node implements Comparable<Node> {
		public int vertex;	
		public long cost;	//	직류 -> 교류 or 교류 -> 직류 변한 횟수
		
		public Node(int vertex, long cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Node other) {
			return Long.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```