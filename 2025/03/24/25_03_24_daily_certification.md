# 25_03_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16118 달빛 여우

[16118번: 달빛 여우](http://boj.ma/16118/t)

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
    private static final long INF = 100_000L * 100_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    
    private static long[] distFox;        //    distFox[i] : 여우가 i 정점 가는 최단 거리
    private static long[][] distWolf;     //    distWolf[mod][i] : 늑대가 i 정점에 홀수번(mod = 0), 짝수번(mod = 1)만에 가는 최단 거리
    
    private static List<Edge>[] graph;
    
    private static int ans;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        distFox = new long[N + 1];
        distWolf = new long[2][N + 1];
        
        graph = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken()) * 2;
            
            graph[v1].add(new Edge(v2, c));
            graph[v2].add(new Edge(v1, c));
        }
        
        dijkstraFox();
        dijkstraWolf();
        
        for(int v = 2; v <= N; v++) {
            long distF = distFox[v];
            long distW = Math.min(distWolf[0][v], distWolf[1][v]);
            
            if(distF < distW)
                ans++;
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static void dijkstraFox() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        Arrays.fill(distFox, INF);
        distFox[1] = 0;
        pq.offer(new Node(1, distFox[1]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            
            if(distFox[cv] < cc)
                continue;
            
            for(Edge edge : graph[cv]) {
                int nv = edge.vertex;
                long nc = distFox[cv] + edge.cost;
                
                if(nc < distFox[nv]) {
                    distFox[nv] = nc;
                    pq.offer(new Node(nv, distFox[nv]));
                }
            }
        }
    }
    
    private static void dijkstraWolf() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        Arrays.fill(distWolf[0], INF);
        Arrays.fill(distWolf[1], INF);
        distWolf[0][1] = 0;
        pq.offer(new Node(1, distWolf[0][1], 0));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            int cm = cur.mod;
            
            if(distWolf[cm][cv] < cc)
                continue;
            
            for(Edge edge : graph[cv]) {
                int nv = edge.vertex;
                long nc = distWolf[cm][cv] + (cm == 0 ? edge.cost / 2: edge.cost * 2);
                int nm = (cm + 1) % 2;
                
                if(nc < distWolf[nm][nv]) {
                    distWolf[nm][nv] = nc;
                    pq.offer(new Node(nv, distWolf[nm][nv], nm));
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
        public int mod;    //    vertex 정점에 홀수번째로 왔으면 1, 짝수번째로 왔으면 0
        
        public Node(int vertex, long cost, int mod) {
            this.vertex = vertex;
            this.cost = cost;
            this.mod = mod;
        }
        
        public Node(int vertex, long cost) {
            this(vertex, cost, 0);
        }
        
        @Override
        public int compareTo(Node other) {
            return Long.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```