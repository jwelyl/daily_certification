# 24_08_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5944 Apple Delivery

[](https://www.acmicpc.net/problem/5944)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int C;        //    간선 개수
    private static int P;        //    정점 개수
    private static int PB;       //    출발점
    private static int PA1;      //    거쳐야 할 점 1
    private static int PA2;      //    거쳐야 할 점 2
    
    private static List<Node>[] graph;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        C = Integer.parseInt(st.nextToken());
        P = Integer.parseInt(st.nextToken());
        PB = Integer.parseInt(st.nextToken());
        PA1 = Integer.parseInt(st.nextToken());
        PA2 = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[P + 1];
        for(int i = 0; i <= P; i++)
            graph[i] = new ArrayList<>();
        
        for(int i = 0; i < C; i++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int cost = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Node(v2, cost));
            graph[v2].add(new Node(v1, cost));
        }
        
        int[] distPB = dijkstra(PB);        //    PB부터 다른 정점까지의 최단 거리
        int[] distPA1 = dijkstra(PA1);      //    PA1부터 다른 정점까지의 최단 거리
        
        //    v1-v2 거리 = v2-v1 거리
        System.out.println(Math.min(distPB[PA1] + distPA1[PA2], distPB[PA2] + distPA1[PA2]));
    }    //    main-end
    
    private static int[] dijkstra(int start) {
        int[] dist = new int[P + 1];
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