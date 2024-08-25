# 24_08_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23807 두 단계 최단 경로 3

[](https://www.acmicpc.net/problem/23807)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final long INF = 300_000L * 1_000_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    
    private static List<Edge>[] graph;    //    그래프
    
    private static long[] distX;    //    시작 정점 X로부터의 최단 거리
    private static long[] distY;    //    중간 정점 Y로부터의 최단 거리
    private static long[] distZ;    //    끝 정점 Z로부터의 최단 거리
    
    private static int X;           //    출발 정점
    private static int Z;           //    도착 정점
    
    private static int P;    //    중간에 거쳐야 하는 3개의 정점 후보
    
    private static int[] midVertices;     //    중간에 거쳐야 하는 정점 배열
    
    private static long ans = INF;    //    중간에 3개 정점 거쳐서 끝 정점에 도착했을 때의 최소 거리
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        
        for(int i = 0; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        distX = new long[N + 1];
        distY = new long[N + 1];
        distZ = new long[N + 1];
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Edge(v2, c));
            graph[v2].add(new Edge(v1, c));
        }
        
        st = new StringTokenizer(br.readLine());
        X = Integer.parseInt(st.nextToken());
        Z = Integer.parseInt(st.nextToken());
        
        P = Integer.parseInt(br.readLine());
        
        midVertices = new int[P];
        st = new StringTokenizer(br.readLine());
        for(int p = 0; p < P; p++)
            midVertices[p] = Integer.parseInt(st.nextToken());
        
        dijkstra(X, distX);
        dijkstra(Z, distZ);
               
        for(int p2 = 0; p2 < P; p2++) {
            int y2 = midVertices[p2];    //    중간에 거쳐야 하는 세 개의 정점 y1, y2, y3 중 두 번째 정점 y2
            dijkstra(y2, distY);         //    y2로부터 각 정점까지의 최단 거리
            
            for(int p1 = 0; p1 < P; p1++) {
                for(int p3 = 0; p3 < P; p3++) {
                    if(p1 == p2 || p2 == p3 || p3 == p1)    //    서로 다른 세 개의 중간 정점 y1, y2, y3 정하기 
                        continue;
                    
                    int y1 = midVertices[p1];
                    int y3 = midVertices[p3];
                    
                    //    X에서 y1까지 갈 수 없거나
                    //    y1에서 y2까지 갈 수 없거나
                    //    y2에서 y3까지 갈 수 없거나
                    //    y3에서 Z까지 갈 수 없을 경우
                    if(distX[y1] == INF || distY[y1] == INF || distY[y3] == INF || distZ[y3] == INF)
                        continue;
                    
                    ans = Math.min(ans, distX[y1] + distY[y1] + distY[y3] + distZ[y3]);
                }
            }
        }
        
        System.out.println(ans == INF ? -1 : ans);
    }    //    main-end
    
    private static void dijkstra(int start, long[] dist) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        Arrays.fill(dist, INF);
        dist[start] = 0;
        
        pq.offer(new Node(start, dist[start]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            
            if(dist[cv] < cc)    //    cv를 거쳐서 가는 것이 개선시키지 못하는 경우
                continue;
            
            for(Edge edge : graph[cv]) {
                int nv = edge.vertex;        //    cv와 인접한 정점
                long nc = cc + edge.cost;    //    nv까지 cv를 거쳐서 가는 최단 거리
                
                if(nc < dist[nv]) {    //    cv를 거쳐서 nv에 가는 게 최단 거리인 경우
                    dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
                }
            }
        }
    }
    
    private static class Edge {
        public int vertex;
        public long cost;
        
        public Edge(int vertex, long cost) {
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