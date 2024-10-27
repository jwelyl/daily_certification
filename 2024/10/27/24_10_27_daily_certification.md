# 24_10_27_daily_certification

```
[#301 koreii] 데일리인증 202410127
1. Java Multithread, Concurrency + OS
- Thread Lifecycle (BLOCKED, WAITING)
- LockSupport
2. 코딩 테스트 대비 알고리즘 학습
- Dijkstra (4123 Bridges and Tunnels)
```

# Problem Solving (Algorithm & SQL)

### BOJ 4123 Bridges and Tunnels

[](https://www.acmicpc.net/problem/4123)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final long INF = 40_000L * 1_000_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	private static int P;	//	쿼리 개수
	
	private static List<Edge>[] graph;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		P = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N];
		for(int v = 0; v < N; v++)
			graph[v] = new ArrayList<>();
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int cost = Integer.parseInt(st.nextToken());
			char io = st.nextToken().charAt(0);
			
			graph[v1].add(new Edge(v2, cost, io));
			graph[v2].add(new Edge(v1, cost, io));
		}
		
		for(int p = 0; p < P; p++) {
			st = new StringTokenizer(br.readLine());
			
			int start = Integer.parseInt(st.nextToken());
			int end = Integer.parseInt(st.nextToken());
			
			dijkstra(start, end);
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dijkstra(int start, int end) {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		long[][] dist = new long[N][2];	//	dist[v][0] : v까지 가기 위해 실외로 가는 최소 비용, dist[v][1] : 총비용
		
		for(int v = 0; v < N; v++) {
			dist[v][0] = INF;
			dist[v][1] = INF;
		}
		
		dist[start][0] = 0;
		dist[start][1] = 0;
		pq.offer(new Node(start, dist[start][0], dist[start][1]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			long cOutCost = cur.outCost;
			long cTotalCost = cur.totalCost;
			
			if(dist[cv][0] < cOutCost || (dist[cv][0] == cOutCost && dist[cv][1] < cTotalCost))
				continue;
			
			for(Edge edge : graph[cv]) {
				int nv = edge.vertex;
				long nOutCost = cOutCost + ((edge.io == 'O') ? edge.cost : 0);
				long nTotalCost = cTotalCost  + edge.cost;
				
				if(nOutCost < dist[nv][0] || (nOutCost == dist[nv][0] && nTotalCost < dist[nv][1])) {
					dist[nv][0] = nOutCost;
					dist[nv][1] = nTotalCost;
					pq.offer(new Node(nv, dist[nv][0], dist[nv][1]));
				}
			}
		}
		
		sb.append(dist[end][0] == INF || dist[end][1] == INF ? "IMPOSSIBLE\n" : String.format("%d %d\n", dist[end][0], dist[end][1]));
	}
	
	private static class Edge {
		public int vertex;
		public int cost;
		public char io;
		
		public Edge(int vertex, int cost, char io) {
			this.vertex = vertex;
			this.cost = cost;
			this.io = io;
		}
	}
	
	private static class Node implements Comparable<Node> {
		public final int vertex;
		public final long outCost;
		public final long totalCost;
		
		public Node(int vertex, long outCost, long totalCost) {
			this.vertex = vertex;
			this.outCost = outCost;
			this.totalCost = totalCost;
		}
		
		@Override
		public int compareTo(Node other) {
			//	밖에서 머무는 시간이 짧을 수록
			int res = Long.compare(this.outCost, other.outCost);
		
			if(res == 0)	//	밖에서 머무는 시간이 같을 경우, 총 시간이 짧을 수록
				res = Long.compare(this.totalCost, other.totalCost);
			
			return res;
		}
	}
}	//	Main-class-end
```