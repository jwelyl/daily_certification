# 24_08_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13911 **집 구하기**

[](https://www.acmicpc.net/problem/13911)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final long INF = 10_000L * 300_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int V;    //    정점 개수
    private static int E;    //    도로 개수
    
    private static int M;    //    맥날 개수
    private static int X;    //    맥세권 조건
    private static int S;    //    스벅 개수
    private static int Y;    //    스세권 조건    
    
    private static List<Edge>[] graph;
    
    private static boolean[] isMcDonald;    //    해당 정점이 맥날이면 true
    private static boolean[] isStarbucks;   //    해당 정점이 스벅이면 true
    
    private static long[] distMC;    //    distMC[i] : i 정점이 집일 경우, 가장 가까운 맥날까지의 거리
    private static long[] distSB;    //    distSB[i] : i 정점이 집일 경우, 가장 가까운 스벅까지의 거리
        
    private static final PriorityQueue<Node> pqMC = new PriorityQueue<>();
    private static final PriorityQueue<Node> pqSB = new PriorityQueue<>();
    
    private static long ans = INF;    //    맥세권, 스세권 만족하는 집 중 두 거리 합이 가장 작은 집의 두 거리 합
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        V = Integer.parseInt(st.nextToken());
        E = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[V + 1];
        for(int i = 0; i <= V; i++)
            graph[i] = new ArrayList<>();
            
        isMcDonald = new boolean[V + 1];
        isStarbucks = new boolean[V + 1];
        
        distMC = new long[V + 1];
        distSB = new long[V + 1];
        Arrays.fill(distMC, INF);
        Arrays.fill(distSB, INF);
        
        for(int e = 0; e < E; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
        
            graph[v1].add(new Edge(v2, c));
            graph[v2].add(new Edge(v1, c));
        }
        
        st = new StringTokenizer(br.readLine());
        M = Integer.parseInt(st.nextToken());
        X = Integer.parseInt(st.nextToken());
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < M; i++) {
            int m = Integer.parseInt(st.nextToken());    //    맥도날드 위치
            isMcDonald[m] = true;
            distMC[m] = 0;
            pqMC.offer(new Node(m, distMC[m]));
        }
        
        st = new StringTokenizer(br.readLine());
        S = Integer.parseInt(st.nextToken());
        Y = Integer.parseInt(st.nextToken());
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < S; i++) {
            int s = Integer.parseInt(st.nextToken());    //    스타벅스 위치
            isStarbucks[s] = true;
            distSB[s] = 0;
            pqSB.offer(new Node(s, distSB[s]));
        }
        
        dijkstra(distMC, pqMC);
        dijkstra(distSB, pqSB);
        
        for(int v = 1; v <= V; v++) {
            if(!isMcDonald[v] && !isStarbucks[v] && distMC[v] <= X && distSB[v] <= Y)
                ans = Math.min(ans, distMC[v] + distSB[v]);
        }
        
        System.out.println(ans == INF ? -1 : ans);
    }    //    main-end
    
    private static void dijkstra(long[] dist, PriorityQueue<Node> pq) {
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            
            if(dist[cv] < cc)
                continue;
            
            for(Edge edge : graph[cv]) {
                int nv = edge.vertex;
                long nc = cc + edge.cost;
                
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