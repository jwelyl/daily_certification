# 25_01_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16681 등산

[16681번: 등산](https://boj.ma/16681/t)

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
	private static final long NONE = Long.MIN_VALUE;
	private static final long INF = 200_000L * 100_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int D;
	private static int E;
	
	private static int[] heights;
	private static List<int[]>[] graph;
	
	private static long answer = NONE;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		D = Integer.parseInt(st.nextToken());
		E = Integer.parseInt(st.nextToken());
		
		heights = new int[N + 1];
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++)
			heights[v] = Integer.parseInt(st.nextToken());
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, c});
			graph[v2].add(new int[] {v1, c});
		}

		//	from[v] : 1에서 v로 높이가 증가하는 방향으로만 이동했을때 최단거리
		long[] from = dijkstra(1);
		//	to[v] : v에서 N으로 높이가 감소하는 방향으로만 이동했을때 최단거
		long[] to = dijkstra(N);
		
		for(int v = 2; v < N; v++) {
			//	1에서 v로 올라갈 수 있고, v에서 N으로 내겨갈 수 있을 경우
			if(from[v] != INF && to[v] != INF) {
				long price = 1L * heights[v] * E;	//	v에 올라서 얻은 가치
				price -= (from[v] + to[v]) * D;		//	체력소모 = (1에서 v까지 가는 거리 + v에서 N까지 가는 거) * D 
			
				answer = Math.max(answer, price);
			}
		}
		
		System.out.println(answer == NONE ? "Impossible" : answer);
	} //	main-end
	
	private static long[] dijkstra(int start) {
		PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
		long[] dist = new long[N + 1];
		Arrays.fill(dist, INF);
		dist[start] = 0;
		pq.offer(new long[] {start, dist[start], 1});
		
		while(!pq.isEmpty()) {
			long[] cur = pq.poll();
			int cv = (int)cur[0];
			long cc = cur[1];
			int ch = (int)cur[2];
			
			if(dist[cv] < cc)
				continue;
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];
				long nc = cc + edge[1];
				
				//	cv에서 nv로 가는게 올라가는 경로이고, cv를 거쳐서 nv로 가는게 기존에 알려진 nv로의 최단거리보다 가까울경우
				if(ch < heights[nv] && nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new long[] {nv, dist[nv], heights[nv]});
				}
			}
		}
		
		return dist;
	}
} //	Main-class-end
```