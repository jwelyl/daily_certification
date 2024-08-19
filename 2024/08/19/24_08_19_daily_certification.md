# 24_08_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17835 면접보는 승범이네

[](https://www.acmicpc.net/problem/17835)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final long INF = 100_000L * 500_000L + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    도시 개수
    private static int M;    //    도로 개수
    private static int K;    //    면접장 개수
    
    private static List<Node>[] graph;         //    면접장들로부터 각 도시까지 최단 거리 구하기 위한 역방향 그래프
    
    private static final PriorityQueue<Node> pq = new PriorityQueue<>();    //    dijkstra pq
    private static long[] dist;                //    dist[i] : 어떤 면접장으로부터 i까지의 최단 거리
    
    private static int farthest;         //    면접장으로부터 가장 먼 도시
    private static long farthestCost;    //    면접장으로부터 가장 먼 도시까지의 비용
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        dist = new long[N + 1];
        
        for(int i = 0; i <= N; i++) {
            dist[i] = INF;
            graph[i] = new ArrayList<>();
        }
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v2].add(new Node(v1, c));    //    역방향 그래프 생성
        }
        
        st = new StringTokenizer(br.readLine());
        for(int k = 0; k < K; k++) {
            int interview = Integer.parseInt(st.nextToken());
            dist[interview] = 0;
            
            pq.offer(new Node(interview, dist[interview]));
        }
        
        dijkstra();
        
        for(int i = 1; i <= N; i++) {
            if(dist[i] > farthestCost) {
                farthestCost = dist[i];
                farthest = i;
            }
        }
        
        System.out.println(farthest);
        System.out.println(farthestCost);
    }    //    main-end
    
    private static void dijkstra() {
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            
            if(dist[cv] < cc)
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                long nc = cc + next.cost;
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
                }
            }
        }
    }
    
    private static class Node implements Comparable<Node> {
        public int vertex;
        public long cost;
        
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