# 24_08_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10652 Piggy Back

[](https://www.acmicpc.net/problem/10652)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int M;        //    간선 개수
    private static int N;        //    정점 개수
    private static long B;        //    Besie가 1만큼 움직이는데 드는 비용
    private static long E;        //    Elssie가 1만큼 움직이는데 드는 비용
    private static long P;        //    Bessie, Elsie가 같이 1만큼 움직이는데 드는 비용
    
    private static List<Node>[] graph;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        B = Integer.parseInt(st.nextToken());
        E = Integer.parseInt(st.nextToken());
        P = Integer.parseInt(st.nextToken());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        for(int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Node(v2, 1));
            graph[v2].add(new Node(v1, 1));
        }
        
        int[] dist1 = dijkstra(1);        //    Bessie 시작점인 1부터 다른 정점까지의 최단 거리
        int[] dist2 = dijkstra(2);        //    Elsie 시작점인 1부터 다른 정점까지의 최단 거리
        int[] distN = dijkstra(N);        //    도착점인 N부터 다른 정점까지의 최단 거리
        
        long ans = B * dist1[N] + E * dist2[N];    //    Bessie, Elsie가 따로 오는 비용
        for(int p = 1; p <= N; p++) {
            long cost = B * dist1[p] + E * dist2[p] + P * distN[p];   //    Bessie, Elise가 p에서 만나서 같이 가는 비용
            
            ans = Math.min(ans, cost);
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static int[] dijkstra(int start) {
        int[] dist = new int[N + 1];
        PriorityQueue<Node> pq = new PriorityQueue<>();
        
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[start] = 0;
        pq.offer(new Node(start, dist[start]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;
            
            if(dist[cv] < cc)    //    cv를 거쳐서 가는게 개선될 여지가 없을 경우
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                int nc = cc + next.cost;    //    cv를 거쳐서 nv를 가는 최단 거리
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
                }
            }
        }
        
        return dist;
    }
    
    private static class Node implements Comparable<Node> {
        public int vertex;    //    정점 번호
        public int cost;      //    비용
        
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