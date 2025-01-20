# 25_01_21_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 31932 나는 북극곰입니다

[31932번: 나는 북극곰입니다](https://boj.ma/31932/t)

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
	private static final long INF = 1_000_000_000L * 100_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	
	private static List<int[]>[] graph;	//	graph[v] : 빙하 v와 연결된 다른 빙하 v'과 얼음 다리 번호
	private static int[] costs;			//	costs[i] : i번째 얼음 다리를 건너는데 필요한 시간
	private static int[] times;			//	times[i] : i번째 얼음 다리가 무너지는 시각
	
	private static int maxTime = 0;		//	가장 늦게 무너지는 시각
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		costs = new int[M + 1];
		times = new int[M + 1];
		
		for(int v = 1; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken());
			int t = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, e});
			graph[v2].add(new int[] {v1, e});
			
			costs[e] = d;
			times[e] = t;
			
			maxTime = Math.max(maxTime, t);
		}
		
		System.out.println(parametricSearch());
	}	//	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = maxTime;
		int res = -1;
		
		while(start <= end) {
			int mid = (start + end) / 2;	//	mid마리만큼 잡아먹는다고 가정
			
			if(dijkstra(mid)) {	//	mid마리만큼 잡아먹고 출발해도 N에 도달할 수 있을 경우
				res = mid;		//	일단 mid 저장
				start = mid + 1;	//	더 잡아먹어도 되는지 확인해보기
			}
			else					//	mid마리만큼 잡아먹고 출바하면 N에 도달할 수 없을 경우
				end = mid - 1;	//	더 조금 잡아먹고 출발해보기
		}
		
		return res;
	}
	
	//	startTime에 1에서 출발했을때 N에 도착 가능하면 true
	private static boolean dijkstra(int startTime) {
		PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
		long[] dist = new long[N + 1];
		Arrays.fill(dist, INF);
		dist[1] = startTime;	//	1에서 startTime에 출발
		
		pq.offer(new long[] {1, dist[1]});
		
		while(!pq.isEmpty()) {
			long[] cur = pq.poll();
			int cv = (int)cur[0];
			long cc = cur[1];
			
			if(cv == N)	//	N에 도착한 경우
				return true;
				
			if(dist[cv] < cc)
				continue;
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];	//	cv와 연결된 빙하
				int e = edge[1];	//	연결된 얼음다리 e
				
				long nc = cc + costs[e];	//	얼음다리 e를 건넌다고 가정할때 건넌 후의 시각
				
				if(nc <= times[e]) {	//	얼음다리 e가 무너지기 전에 건널 수 있을 경우
					if(nc < dist[nv]) {
						dist[nv] = nc;
						pq.offer(new long[] {nv, dist[nv]});
					}
				}
			}
		}
		
		return false;	//	N에 도착할 수 없음
	}
}	//	Main-class-end
```