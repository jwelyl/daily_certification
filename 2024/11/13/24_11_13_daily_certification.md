# 24_11_13_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 5917 Roadblock**

[5917번: Roadblock](http://boj.ma/5917/t)

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
	private static final long INF = 10_000L * 1_000_000L + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	
	private static long origMinDist;	//	최초 그래프에서의 1에서 N까지의 최단거리
	private static final List<Integer> shortestPathEdges = new ArrayList<>();	//	최초 최단거리에 포함되는 간선 번호 
	private static int[] prevs;	//	prevs[v] : 1을 시작점으로 하는 shortest path tree에서 v의 이전 정점, 1일 경우 0 
	
	private static long maxDiff = Integer.MIN_VALUE;
	
	private static List<Edge>[] graph;
	private static int[] edgeCosts;		//	edgeCosts[e] : e번째 간선의 간선 비용
	private static int[][] edgeNums;	//	edgeNums[i][j] = edgeNums[j][i] : 두 정점 i, j를 연결하는 간선 번호
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		edgeCosts = new int[M + 1];
		edgeNums = new int[N + 1][N + 1];
		prevs = new int[N + 1];
		
		for (int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			edgeCosts[e] = c;
			edgeNums[v1][v2] = e;
			edgeNums[v2][v1] = e;
			graph[v1].add(new Edge(v2, e));
			graph[v2].add(new Edge(v1, e));
		}
		
		origMinDist = dijkstra(true);	//	최초 다익스트라
		
		for(int eNum : shortestPathEdges) {
			edgeCosts[eNum] *= 2;	//	최초 최단거리에 포함되는 간선 길이 2배
			
			maxDiff = Math.max(maxDiff, dijkstra(false) - origMinDist);
			
			edgeCosts[eNum] /= 2;	//	최초 최단거리에 포함되는 간선 길이 원상복구
		}
		
		System.out.println(maxDiff);
	}	//	main-end
	
	private static long dijkstra(boolean initial) {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		long[] dist = new long[N + 1];
		Arrays.fill(dist, INF);
		
		dist[1] = 0;
		pq.offer(new Node(1, dist[1]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			long cc = cur.cost;
			
			if(dist[cv] < cc)
				continue;
			
			for(Edge edge : graph[cv]) {
				int nv = edge.vertex;
				long nc = cc + edgeCosts[edge.edgeNum];
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new Node(nv, dist[nv]));
					prevs[nv] = cv;
				}
			}
		}
		
		if(initial) {
            int cur = N;

            while(cur != 1) {
                int prev = prevs[cur];

                shortestPathEdges.add(edgeNums[cur][prev]);
                cur = prev;
            }
		}
		
		return dist[N];
	}

	private static class Edge {
		public int vertex;
		public int edgeNum;
		
		public Edge(int vertex, int edgeNum) {
			this.vertex = vertex;
			this.edgeNum = edgeNum;
		}
	}
	
	private static class Node implements Comparable<Node> {
		public int vertex;
		public long cost;
		
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