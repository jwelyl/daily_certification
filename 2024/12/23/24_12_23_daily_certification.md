# 24_12_23_daily_certification

### **BOJ 2398 3인통화**

[2398번: 3인통화](https://boj.ma/2398/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;

public class Main {
    private static final int INF = 100 * 1_000 * 1_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    private static int[][] edgeCosts;
    private static int[][] used;
    
    private static int V1;
    private static int V2;
    private static int V3;

    private static int[] dist1;
    private static int[] dist2;
    private static int[] dist3;
    
    private static int minSum = INF;
    private static int minSumV = -1;
    
    private static int minCost = 0;
    private static int minCostEdgeCnt = 0;
    private static List<int[]> minCostEdges = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        edgeCosts = new int[N + 1][N + 1];
        used = new int[N + 1][N + 1];
        
        dist1 = new int[N + 1];
        dist2 = new int[N + 1];
        dist3 = new int[N + 1];
        
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(v2);
            graph[v2].add(v1);
            
            edgeCosts[v1][v2] = c;
            edgeCosts[v2][v1] = c;
        }
        
        st = new StringTokenizer(br.readLine());
        V1 = Integer.parseInt(st.nextToken());
        V2 = Integer.parseInt(st.nextToken());
        V3 = Integer.parseInt(st.nextToken());

        dijkstra(V1, dist1, null);
        dijkstra(V2, dist2, null);
        dijkstra(V3, dist3, null);
        
        for(int v = 1; v <= N; v++) {
        	if(dist1[v] + dist2[v] + dist3[v] < minSum) {
        		minSum = dist1[v] + dist2[v] + dist3[v];
        		minSumV = v;
        	}
        }
        
        dijkstra(minSumV, dist1, new int[N + 1]);
        
        sb.append(minCost).append(" ").append(minCostEdgeCnt).append("\n");
        for(int[] edge : minCostEdges)
            sb.append(edge[0]).append(" ").append(edge[1]).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dijkstra(int start, int[] dist, int[] prev) {
    	PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
        Arrays.fill(dist, INF);
        dist[start] = 0;
        if(prev != null)
        	prev[start] = start;
        
        pq.offer(new int[] {start, dist[start]});
        
        while(!pq.isEmpty()) {
            int[] cur = pq.poll();
            int cv = cur[0];
            int cc = cur[1];
            
            if(dist[cv] < cc)
                continue;
            
            for(int nv : graph[cv]) {
                int nc = cc + edgeCosts[cv][nv];
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    if(prev != null)
                    	prev[nv] = cv;
                    pq.offer(new int[] {nv, dist[nv]});
                }
            }
        }
        
        if(prev != null)
        	shortestPathTree(new int[] {V1, V2, V3}, prev);
    }
    
    private static void shortestPathTree(int[] vertices, int[] prev) {
    	for(int cur : vertices) {
    		while(cur != minSumV) {
    			int parent = prev[cur];
    			
    			used[parent][cur]++;
    			used[cur][parent]++;
    			
    			if(used[parent][cur] == 1 && used[cur][parent] == 1) {
    				minCost += edgeCosts[parent][cur];
    				minCostEdgeCnt++;
    				minCostEdges.add(new int[] {parent, cur});
    			}
    			
    			cur = parent;
    		}
    	}
    }
}    //    Main-class-end

```