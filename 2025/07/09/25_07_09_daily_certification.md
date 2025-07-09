# 25_07_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8452 그래프와 쿼리

[8452번: 그래프와 쿼리](http://boj.ma/8452/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	private static int Q;	//	질의 수
	
	private static List<Integer>[] graph;
	private static Edge[] edges;
	private static Query[] queries;
	private static int[] answer;
	private static int[] dist;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		edges = new Edge[M + 1];
		queries = new Query[Q];
		answer = new int[Q];
		
		for(int eNum = 1; eNum <= M; eNum++) {
			st = new StringTokenizer(br.readLine());
			int from = Integer.parseInt(st.nextToken());
			int to = Integer.parseInt(st.nextToken());
			
			edges[eNum] = new Edge(from, to, false);
		}
		
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			char opt = st.nextToken().charAt(0);
			int num = Integer.parseInt(st.nextToken());
			
			queries[q] = new Query(opt, num);
			
			if(opt == 'U')
				edges[num].deleted = true;
		}
		
		for(int eNum = 1; eNum <= M; eNum++) {
			if (!edges[eNum].deleted) {
				int from = edges[eNum].from;
				int to = edges[eNum].to;
				graph[from].add(to);
			}
		}
		
		dist = new int[N + 1];
		Arrays.fill(dist, INF);
		dist[1] = 0;
		
		Queue<Integer> queue = new LinkedList<>();
		queue.offer(1);
		
		//	쿼리에서 제거되는 모든 간선이 제거된 상태로 1번 정점부터 BFS
		while (!queue.isEmpty()) {
			int cur = queue.poll();
			for (int next : graph[cur]) {
				if (dist[cur] + 1 < dist[next]) {
					dist[next] = dist[cur] + 1;
					queue.add(next);
				}
			}
		}
		
		for(int q = Q - 1; q >= 0; q--) {	//	쿼리 역순으로 처리
			Query query = queries[q];
			char opt = query.opt;
			int num = query.num;
			
			if (opt == 'E')	//	이전까지 연결된 상태로 num까지의 최단거리
				answer[q] = dist[num];
			else {
				int from = edges[num].from;
				int to = edges[num].to;			//	추가되는 간선 (from, to)
				
				graph[from].add(to);
				
				if(dist[from] != INF) {	//	from까지는 도달 가능할 경우	
					if(dist[from] + 1 < dist[to]) {	//	from까지 간 후 to로 가는 것이, from을 거치지 않고 to로 가는것보다 빠를 경우
						dist[to] = dist[from] + 1;				//	to까지의 거리 갱신
						queue.offer(to);						//	to에서부터 도달 가능한 정점 찾기
						while (!queue.isEmpty()) {
							int cur = queue.poll();
							for (int next : graph[cur]) {
								if (dist[cur] + 1 < dist[next]) {
									dist[next] = dist[cur] + 1;
									queue.add(next);
								}
							}
						}
					}
				}
			}
		}
		
		for (int q = 0; q < Q; q++) {
			if (queries[q].opt == 'E')
				sb.append(answer[q] == INF ? -1 : answer[q]).append('\n');
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static class Query {
		public char opt;
		public int num;
		
		public Query(char opt, int num) {
			this.opt = opt;
			this.num = num;
		}
	}
	
	private static class Edge {
		public int from;
		public int to;
		public boolean deleted;
		
		public Edge(int from, int to, boolean deleted) {
			this.from = from;
			this.to = to;
			this.deleted = deleted;
		}
	}
} //	Main-class-end
```