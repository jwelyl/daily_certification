# 25_11_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22870 산책 (Large)

[22870번: 산책 (large)](http://boj.ma/22870/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;

public class Main {
	private static final int INF = 1_000 * 500_000 + 1;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    private static int S;    //    출발 정점
    private static int E;    //    도착 정점
    
    private static List<Edge>[] graph;
    private static int[] distS;
    private static int[] distE;
    private static boolean[] blocked;
    private static int answer = 0;    //    S->E까지 최단거리/사전순으로 빠른 순으로 이동 후 E->S까지 이동한 거리 합
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        distS = new int[N + 1];
        distE = new int[N + 1];
        blocked = new boolean[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int cost = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Edge(v2, cost));
            graph[v2].add(new Edge(v1, cost));
        }
        
        st = new StringTokenizer(br.readLine());
        S = Integer.parseInt(st.nextToken());
        E = Integer.parseInt(st.nextToken());
        
        dijkstra(S, distS, false);	//	시작 정점에서 각 정점까지의 최단거리
        dijkstra(E, distE, false);	//	도착 정점에서 각 정점까지의 최단거리
        answer = distS[E];	//	일단 아무 제약 없는 상태에서 최단거리 더하기
        
        dfs(S, 0, new boolean[N + 1]);
        blocked[S] = false;
        blocked[E] = false;
        dijkstra(E, distE, true);	//	S->E 경로에 포함되는 정점 제외하고 E->S 최단거리 구하기
        answer += distE[S];
        
        System.out.println(answer);
    }    //    main-end
    
    private static void dfs(int cur, int distSum, boolean[] visited) {
    	if(cur == E)
    		return;
    	
    	visited[cur] = true;
    	int firstNext = INF;
    	int fistNextCost = INF;
    	
    	for(Edge edge : graph[cur]) {
    		int next = edge.vertex;
    		
    		if(!visited[next] && distSum + edge.cost + distE[next] == distS[E]) {
    			
    			if(firstNext > next) {
    				firstNext = next;
    				fistNextCost = edge.cost;
    			}
    		}
    	}
    	
		blocked[firstNext] = true;
		dfs(firstNext, distSum + fistNextCost, visited);
    }
    
    private static void dijkstra(int start, int[] dist, boolean limit) {
        PriorityQueue<Node> pq = new PriorityQueue<>();

        Arrays.fill(dist, INF);
        dist[start] = 0;
        pq.offer(new Node(start, dist[start]));
        
       while(!pq.isEmpty()) {
    	   Node cur = pq.poll();
    	   int cv = cur.vertex;
    	   int cc = cur.cost;
    	   
    	   if(dist[cv] < cc)
    		   continue;
    	   
    	   for(Edge edge : graph[cv]) {
    		   int nv = edge.vertex;
    		   int nc = cc + edge.cost;
    		   
    		   if(limit && blocked[nv])
    			   continue;
    		   
    		   if(nc < dist[nv]) {
    			   dist[nv] = nc;
    			   pq.offer(new Node(nv, dist[nv]));
    		   }
    	   }
       }
    }
    
    private static class Edge {
    	public int vertex;
    	public int cost;
    	
    	public Edge(int vertex, int cost) {
    		this.vertex = vertex;
    		this.cost = cost;
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