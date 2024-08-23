# 24_08_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23801 두 단계 최단 경로 2

[](https://www.acmicpc.net/problem/23801)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;        //    정점 개수
    private static int M;        //    간선 개수
    private static int S;		 //	   출발 정점
    private static int E;		 //	   도착 정점
    private static int P;		 //	   중간 정점 개수
    private static long ans = Long.MAX_VALUE;	//	최단 거리
    
    private static List<Node>[] graph;
    private static long[] distS;	//	S로부터 다른 정점까지의 거리
    private static long[] distE;	//	E로부터 다른 정점까지의 거리/
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        distS = new long[N + 1];
        distE = new long[N + 1];
        
        for(int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int cost  = Integer.parseInt(st.nextToken());
           
            graph[v1].add(new Node(v2, cost));
            graph[v2].add(new Node(v1, cost));
        }
        
        st = new StringTokenizer(br.readLine());
        S = Integer.parseInt(st.nextToken());
        E = Integer.parseInt(st.nextToken());
        
        dijkstra(S, distS);
        dijkstra(E, distE);
        
        P = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        for(int p = 0; p < P; p++) {
        	int mid = Integer.parseInt(st.nextToken());	//	중간 정점
        	
        	//	S에서 mid까지 도달할 수 없거나, mid에서 E까지 도달할 수 없는 경우
        	if(distS[mid] == Long.MAX_VALUE || distE[mid] == Long.MAX_VALUE)
        		continue;
        	
        	ans = Math.min(ans, distS[mid] + distE[mid]);
        }
        
        System.out.println(ans == Long.MAX_VALUE ? -1 : ans);
    }    //    main-end
    
    private static void dijkstra(int start, long[] dist) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[start] = 0;
        pq.offer(new Node(start, dist[start]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            
            if(dist[cv] < cc)    //    cv를 거쳐서 가는게 개선될 여지가 없을 경우
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                long nc = cc + next.cost;    //    cv를 거쳐서 nv를 가는 최단 거리
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
                }
            }
        }
    }
    
    private static class Node implements Comparable<Node> {
        public int vertex;    //    정점 번호
        public long cost;     //    비용
        
        public Node(int vertex, long cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            return Long.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```