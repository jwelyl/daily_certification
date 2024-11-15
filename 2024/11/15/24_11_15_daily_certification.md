# 24_11_15_daily_certification

```
[#320 koreii] 데일리인증 20241115
1. 최종 면접 완료 + 후기
2. 컬쳐핏 면접 준비
3. Spring Core Advanced
- @Aspect
- 종단 관심사 vs 횡단 관심사, 핵심 기능 vs 부가 기능
- AOP, AOP 용어 정리
4. 코딩 테스트 대비 알고리즘 학습
- Multisource BFS, Dijkstra (11952 좀비)
```

# Problem Solving (Algorithm & SQL)

### **BOJ** 14167 Moocast

[11952번: 좀비](https://boj.ma/11952/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 200_001;
	private static final long INF = 200_000L * 100_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;		//	도시 수
	private static int M;		//	길의 수
	private static int K;		//	점령당한 도시 수
	private static int S;		//	위험한 도시 범위
	
	private static int P;		//	안전한 도시 숙박 비용
	private static int Q;		//	위험한 도시 숙박 비용

	private static final Queue<int[]> q = new LinkedList<>();
	private static int[] dist;	//	점령당한 도시로부터의 최단거리
	
	private static List<Integer>[] graph;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
	    K = Integer.parseInt(st.nextToken());
		S = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		P = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		dist = new int[N + 1];
		Arrays.fill(dist, MAX);
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int k = 0; k < K; k++) {
			int vertex = Integer.parseInt(br.readLine());	//	점령당한 도시
			dist[vertex] = 0;
			q.offer(new int[] {vertex, dist[vertex]});
		}
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			graph[v1].add(v2);
			graph[v2].add(v1);
		}
		
		bfs();
		
		System.out.println(dijkstra());
	}	//	main-end
	
	private static void bfs() {
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cv = cur[0];
			int cc = cur[1];
			
			for(int nv: graph[cv]) {
				int nc = cc + 1;
				
				if(dist[nv] == MAX) {
					dist[nv] = nc;
					q.offer(new int[] {nv, dist[nv]});
				}
			}
		}
	}
	
	private static long dijkstra() {
		final long[] distances = new long[N + 1];
		final PriorityQueue<Node> pq = new PriorityQueue<>((node1, node2) -> Long.compare(node1.cost, node2.cost));
		
		Arrays.fill(distances, INF);
		distances[1] = 0;
		pq.offer(new Node(1, distances[1]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			long cc = cur.cost;
			
			if(distances[cv] < cc)
				continue;
			
			for(int nv: graph[cv]) {
				long nc = cc + ((dist[nv] <= S) ? Q : P);
				
				if(dist[nv] == 0)	//	점령당한 도시는 갈 수 없음
					continue;
				
				if(nc < distances[nv]) {
					distances[nv] = nc;
					pq.offer(new Node(nv, distances[nv]));
				}
			}
		}
		
		distances[N] -= dist[N] <= S ? Q : P;
		return distances[N];
	}
	
	private static class Node {
		public int vertex;
		public long cost;
		
		public Node(int vertex, long cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
	}
}	//	Main-class-end
```