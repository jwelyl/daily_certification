# 24_10_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14618 총깡 총깡

[](https://www.acmicpc.net/problem/14618)

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
	private static final int INF = 100 * 20_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;		//	집 개수
	private static int M;		//	간선 개수
	private static int J;		//	출발점
	private static int K;		//	각 동물 수
	
	private static int[] A;		//	A 집 위치
	private static int[] B;		//	B 집 위치
	
	private static List<Edge>[] graph;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		J = Integer.parseInt(br.readLine());
		K = Integer.parseInt(br.readLine());
		
		A = new int[K];
		B = new int[K];
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		st = new StringTokenizer(br.readLine());
		for(int k = 0; k < K; k++)
			A[k] = Integer.parseInt(st.nextToken());
		st = new StringTokenizer(br.readLine());
		for(int k = 0; k < K; k++)
			B[k] = Integer.parseInt(st.nextToken());
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new Edge(v2, c));
			graph[v2].add(new Edge(v1, c));
		}
		
		dijkstra();
	}	//	main-end
	
	private static void dijkstra() {
		//	{v, c} : v까지 가는 최단거리 c
		PriorityQueue<int[]> pq = new PriorityQueue<>((o1, o2) -> Integer.compare(o1[1], o2[1]));
		int[] dist = new int[N + 1];
		Arrays.fill(dist, INF);
		dist[J] = 0;
		
		pq.offer(new int[] {J, dist[J]});
		
		while(!pq.isEmpty()) {
			int[] cur = pq.poll();
			int cv = cur[0];
			int cc = cur[1];
			
			if(dist[cv] < cc)
				continue;
			
			for(Edge edge : graph[cv]) {
				int nv = edge.vertex;
				int nc = cc + edge.cost;
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new int[] {nv, dist[nv]});
				}
			}
		}
		
		int minDist = INF;
		char minDistSort = ' ';
		
		//	A에 속한 집으로부터 J까지의 최단거리
		for(int k = 0; k < K; k++) {
			int d = dist[A[k]];
			
			if(d <= minDist) {	//	최단거리를 갱신하거나 같을 경우
				minDist = d;
				minDistSort = 'A';	//	A를 우선함
			}
		}
		//	B에 속한 집으로부터 J까지의 최단거리
		for(int k = 0; k < K; k++) {
			int d = dist[B[k]];
			
			if(d < minDist) {	//	최단거리를 갱신할 경우
				minDist = d;
				minDistSort = 'B';
			}
		}
		
		if(minDist == INF)
			System.out.println(-1);
		else {
			System.out.println(minDistSort);
			System.out.println(minDist);
		}
	}
	
	private static class Edge {
		public final int vertex;
		public final int cost;
		
		public Edge(int vertex, int cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
	}
}	//	Main-class-end
```