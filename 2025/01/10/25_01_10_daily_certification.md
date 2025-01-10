# 25_01_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10715 JOI 공원

[10715번: JOI 공원](https://boj.ma/10715/t)

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
	private static final long INF = 200_000L * 100_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in)) ;
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int C;
	
	private static List<int[]>[] graph;
	private static long costSum = 0;
	private static long answer = Long.MAX_VALUE;
 	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, c});
			graph[v2].add(new int[] {v1, c});
			costSum += c;
		}
	
		dijkstra();
		
		System.out.println(answer);
	} //	main-end
	
	private static void dijkstra() {
		PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
		long[] dist = new long[N + 1];
		boolean[] visited = new boolean[N + 1];
		
		Arrays.fill(dist, INF);
		dist[1] = 0;
		
		pq.offer(new long[] {1, dist[1]});
		
		while(!pq.isEmpty()) {
			long[] cur = pq.poll();
			int cv = (int)cur[0];
			long cc = cur[1];
			
			if(visited[cv])	//	이미 최단거리가 알려졌으면
				continue;
			if(dist[cv] < cc)
				continue;
			
			visited[cv] = true;
			
			for(int[] next : graph[cv]) {	//	cv와 연결된 정점들에 대하여
				int nv = next[0];			//	인접한 정점
				long nc = next[1];			//	cv, nv 사이 간선 비용
				
				if(visited[nv])				//	이미 nv의 최단거리도 알려진 경우
					costSum -= nc;			//	둘 사이의 간선은 제거되어야 함
			}
			
			answer = Math.min(answer, cc * C + costSum);
			
			for(int[] next : graph[cv]) {
				int nv = next[0];			//	인접한 정점
				long nc = cc + next[1];		//	cv를 거쳐서 nv로 가는 최단거리
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new long[] {nv, nc});
				}
			}
		}
	}
} //	Main-class-end
```