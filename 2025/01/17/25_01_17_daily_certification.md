# 25_01_17_daily_certification

```
[#017 koreii] 데일리인증 20250117
1. 프로젝트 리팩토링
- 회원 관련 Repository 인터페이스 구현 중
- Service 계층 구현 중
2. 코딩 테스트 대비 알고리즘 학습
- Dijkstra, DP (BOJ 13907 세금)
```

# Problem Solving (Algorithm & SQL)

### **BOJ 13907** 세금

[13907번: 세금](https://boj.ma/13907/t)

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
	private static final int INF = 30_000 * 1_000 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	도시 수
	private static int M;	//	도로 수
	private static int K;	//	세금 인상 횟수
	
	private static int S;	//	출발 정점
	private static int D;	//  도착 정점
	
	private static List<int[]>[] graph;
	private static int[] adds;	//	adds[i] : i번째 세금 인상때 인상되는 세금량
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		S = Integer.parseInt(st.nextToken());
		D = Integer.parseInt(st.nextToken());

		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		adds = new int[K + 1];
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, c});
			graph[v2].add(new int[] {v1, c});
		}
		
		for(int k = 1; k <= K; k++)
			adds[k] = Integer.parseInt(br.readLine());
		
		dijkstra();
		
		System.out.print(sb);
	}	//	main-end
	
	private static void dijkstra() {
		PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
		int[][] dist = new int[N][N + 1];	//	dist[cnt][v] : S에서 v까지 cnt개의 간선을 거쳐갔을때 최소비용
		int res = INF;
		
		for(int cnt = 0; cnt < N; cnt++)
			Arrays.fill(dist[cnt], INF);
		
		dist[0][S] = 0;
		pq.offer(new int[] {S, dist[0][S], 0});
		
		while(!pq.isEmpty()) {
			int[] cur = pq.poll();
			int cv = cur[0];
			int cc = cur[1];
			int ccnt = cur[2];	//	S에서 cv까지 ccnt개의 간선을 거쳐서 cc의 비용으로 옴
			
			if(dist[ccnt][cv] < cc)
				continue;
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];
				
				boolean ok = true;
				for(int i = 0; i <= ccnt; i++) {
					//	S에서 i개의 간선을 거쳐서 nv로 오는 비용이
					//	S에서 cv까지 ccnt의 간선을 거쳐서 온 뒤에 간선을 하나 더 거쳐서 nv로 오는 것보다 쌀 경우
					if(dist[i][nv] < dist[ccnt][cv] + edge[1]) {
						ok = false;
						break;
					}
				}
				
				if(ok && ccnt + 1 < N) {
					if(dist[ccnt][cv] + edge[1] < dist[ccnt + 1][nv]) {
						dist[ccnt + 1][nv] = dist[ccnt][cv] + edge[1];
						pq.offer(new int[] {nv, dist[ccnt + 1][nv], ccnt + 1});
					}
				}
			}
		}
		
		int add = 0;	//	세금 k회 인상될때마다 각 간선에 더해질 누적비용
		for(int k = 0; k <= K; k++) {	//	세금 k회 인상되었을때 S에서 D까지 최소비용
			add += adds[k];
			
			//	cnt개의 간선을 거쳐서 D로 갈 경우 기존에 구했던 dist값에 add * cnt의 비용이 추가됨
			for(int cnt = 1; cnt < N; cnt++)
				res = Math.min(res, cnt * add + dist[cnt][D]);
			
			sb.append(res).append("\n");
			res = INF;
		}
	}
}	//	Main-class-end
```