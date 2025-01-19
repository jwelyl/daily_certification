# 25_01_19_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 18262 Milk Pumping

[18262번: Milk Pumping](https://boj.ma/18262/t)

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
	private static final int INF = 1_000 * 1_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in)) ;
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static List<int[]>[] graph;
	private static int[] flows;
	
	private static int answer = 0;
 	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		flows = new int[M];
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			int f = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, c, f});
			graph[v2].add(new int[] {v1, c, f});
			flows[e] = f;
		}
	
		for(int e = 0; e < M; e++)
			dijkstra(flows[e]);
		
		System.out.println(answer);
	} //	main-end
	
	//	flowLimit보다 작은 유량의 파이프로는 갈수 없음
	private static void dijkstra(int flowLimit) {
		PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
		int[] dist = new int[N + 1];
		boolean isMinFlow = false;	//	flowLimit이 정말 경로 유량인지, 최단경로 중에 flowLimit이 있는지 확인
		
		Arrays.fill(dist, INF);
		dist[1] = 0;
		
		pq.offer(new int[] {1, dist[1]});
		
		while(!pq.isEmpty()) {
			int[] cur = pq.poll();
			int cv = (int)cur[0];
			int cc = cur[1];
			
			if(dist[cv] < cc)
				continue;
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];			//	인접한 정점
				int nc = cc + edge[1];		//	cv를 거쳐서 nv로 가는 최단거리
				int flow = edge[2];			//	파이프 (cv, nv)의 유량
				
				if(flow < flowLimit)		//	제한 유량보다 적을 경우 갈수 없음
					continue;
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new int[] {nv, nc});
				
					if(flow == flowLimit)
						isMinFlow = true;
				}
			}
		}
		
		if(dist[N] != INF && isMinFlow)
			answer = Math.max(answer, (flowLimit * 1_000_000) / dist[N]);
	}
} //	Main-class-end
```