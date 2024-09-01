# 24_09_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2176 합리적인 이동경로

[](https://www.acmicpc.net/problem/2176)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int NONE = 1_000 * 100_000 + 1;
    
    private static final int S = 1;
    private static final int T = 2;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    
    private static List<Node>[] graph;
    
    private static int[] dist;    //    T로부터 다른 정점까지의 최단 거리
    
    private static int[] dp;    //    dp[v] : S부터 v까지 합리적인 이동 경로로 이동하는 경우의 수
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++)
            graph[i] = new ArrayList<>();
        dist = new int[N + 1];
        dp = new int[N + 1];
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Node(v2, c));
            graph[v2].add(new Node(v1, c));
        }
        
        dijkstra();
        
        System.out.println(dp());
    }    //    main-end
    
    private static int dp() {
    	//	{v, dist[v]} 저장, dist[v]가 작은 것이 우선순위가 높음
    	PriorityQueue<int[]> pq = new PriorityQueue<>(new Comparator<int[]>() {
			@Override
			public int compare(int[] o1, int[] o2) {
				return Integer.compare(o1[1], o2[1]);
			}
    	});
    	
    	for(int v = 1; v <= N; v++)
    		pq.offer(new int[] {v, dist[v]});
    	
    	pq.poll();
    	
    	dp[T] = 1;	//	T에서 T까지의 합리적인 경로 수 = 1
    	
    	while(!pq.isEmpty()) {
    		int[] cur = pq.poll();
    		int cv = cur[0];	//	pq에 있는 정점 중 T로부터 가장 가까운 정점 
    		
    		for(Node edge : graph[cv]) {	//	cv와 연결된 정점들 순회
    			int nv = edge.vertex;
    			
    			if(dist[nv] < dist[cv])	//	cv보다 nv가 더 가까울 경우
    				dp[cv] += dp[nv];
    		}
    		
    		if(cv == 1)
    			break;
    	}
    	
    	return dp[S];
    }
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        Arrays.fill(dist, NONE);
        dist[T] = 0;
        pq.offer(new Node(T, dist[T]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;
            
            if(dist[cv] < cc)
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                int nc = cc + next.cost;
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
                }
            }
        }
    }
    
    private static class Node implements Comparable<Node> {
        public int vertex;
        public int cost;
        
        public Node(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```