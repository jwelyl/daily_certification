# 25_01_16_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 14285** 간선 끊어가기

[14285번: 간선 끊어가기](https://boj.ma/14285/t)

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
	private static final int INF = 100_000 * 100 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int S;
	private static int T;
	private static List<Integer>[] graph;
	
	private static int costSum = 0;			//	최초에 주어진 모든 간선 길이 합
	private static Edge[] edges;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		edges = new Edge[M + 1];
		
		for(int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int cost = Integer.parseInt(st.nextToken());
			
			edges[e] = new Edge(v1, v2, cost);
			graph[v1].add(e);
			graph[v2].add(e);
			
			costSum += cost;
		}
		
		st = new StringTokenizer(br.readLine());
		S = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		int[] distS = dijkstra(S, T);
		int[] distT = dijkstra(T, S);
		int minCost = INF;
		
		for(int e = 1; e <= M; e++) {
			Edge edge = edges[e];
			int v1 = edge.v1;
			int v2 = edge.v2;	//	edge를 제거한다고 가정
			
			//	S에서 v1까지 가는 최단거리 + v2에서 T까지 가는 최단거리
			minCost = Math.min(minCost, distS[v1] + distT[v2]);
			//	T에서 v1까지 가는 최단거리 + v2에서 S까지 가는 최단거리
			minCost = Math.min(minCost, distT[v1] + distS[v2]);
		}
		
		System.out.println(costSum - minCost);
	}	//	main-end
	
	private static int[] dijkstra(int start, int end) {
		PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
		int[] dist = new int[N + 1];
		Arrays.fill(dist, INF);
		dist[start] = 0;
		
		pq.offer(new int[] {start, dist[start]});
		
		while(!pq.isEmpty()) {
			int[] cur = pq.poll();
			int cv = cur[0];
			int cc = cur[1];
			
			if(cv == end)
				break;
			
			if(dist[cv] < cc)
				continue;
			
			for(int e : graph[cv]) {
				Edge edge = edges[e];
				int nv = edge.v1 == cv ? edge.v2 : edge.v1;
				int nc = cc + edge.cost;
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new int[] {nv, dist[nv]});
				}
			}
		}
		
		return dist;
	}
	
	private static class Edge {
		public int v1;
		public int v2;
		public int cost;
		
		public Edge(int v1, int v2, int cost) {
			this.v1 = v1;
			this.v2 = v2;
			this.cost = cost;
		}
	}
}	//	Main-class-end
```

### **BOJ 2632** 피자판매

[2632번: 피자판매](http://boj.ma/2632/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.math.BigDecimal;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int T;
    private static int M;
    private static int N;
    
    private static int[] prefixSumA;
    private static int[] prefixSumB;
    
    private static final Map<Integer, Integer> aMap = new HashMap<>();
    private static final Map<Integer, Integer> bMap = new HashMap<>();
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
    	T = Integer.parseInt(br.readLine());
    	
    	st = new StringTokenizer(br.readLine());
    	M = Integer.parseInt(st.nextToken());
    	N = Integer.parseInt(st.nextToken());
    	
    	prefixSumA = new int[M + 1];
    	prefixSumB = new int[N + 1];
    	
    	for(int m = 1; m <= M; m++)
    		prefixSumA[m] = prefixSumA[m - 1] + Integer.parseInt(br.readLine());
    	for(int n = 1; n <= N; n++)
    		prefixSumB[n] = prefixSumB[n - 1] + Integer.parseInt(br.readLine());
    	
    	aMap.put(0, 1);
    	bMap.put(0, 1);
    	
    	for(int start = 1; start <= M; start++) {
    		for(int end = 1; end <= M; end++) {
    			if(start == end + 1)
    				continue;
    			
    			int ps = partialSum(prefixSumA, start, end);
    			int cnt = aMap.getOrDefault(ps, 0) + 1;
    			aMap.put(ps, cnt);
    		}
    	}
    	
    	for(int start = 1; start <= N; start++) {
    		for(int end = 1; end <= N; end++) {
    			if(start == end + 1)
    				continue;
    			
    			int ps = partialSum(prefixSumB, start, end);
    			int cnt = bMap.getOrDefault(ps, 0) + 1;
    			bMap.put(ps, cnt);
    		}
    	}
    	
    	for(Map.Entry<Integer, Integer> entry : aMap.entrySet()) {
    		int sum = entry.getKey();
    		long cnt1 = entry.getValue();
    		long cnt2 = bMap.getOrDefault(T - sum, 0);
    		
    		answer += cnt1 * cnt2;
    	}
    	
    	System.out.println(answer);
    }	//	main-end
    
    private static int partialSum(int[] prefixSum, int start, int end) {
    	if(end < start)
    		return prefixSum[prefixSum.length - 1] - prefixSum[start - 1] + prefixSum[end];
    	
    	return prefixSum[end] - prefixSum[start - 1];
    }
}    //    Main-class-end
```