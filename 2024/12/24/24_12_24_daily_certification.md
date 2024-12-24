# 24_12_24_daily_certification

### **BOJ 17940 지하철**

[17940번: 지하철](https://boj.ma/17940/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;

public class Main {
    private static final int INF = 1_000 * 1_000 * 1_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int E;
    private static boolean[] companies;
    private static List<int[]>[] graph;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        E = Integer.parseInt(st.nextToken());
        
        companies = new boolean[N];
        graph = new ArrayList[N];
        for(int v = 0; v < N; v++)
            graph[v] = new ArrayList<>();
        
        for(int v = 0; v < N; v++)
            companies[v] = Integer.parseInt(br.readLine()) == 1;
        
        for(int v1 = 0; v1 < N; v1++) {
            st = new StringTokenizer(br.readLine());
            for(int v2 = 0; v2 < N; v2++) {
                int c = Integer.parseInt(st.nextToken());
                
                if(c != 0)
                    graph[v1].add(new int[] {v2, c});
            }
        }
        
        dijkstra();
    }    //    main-end
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        Node[] dist = new Node[N];
        for(int v = 0; v < N; v++)
            dist[v] = new Node(v, INF, INF);
        
        dist[0].cnt = 0;
        dist[0].cost = 0;
        
        pq.offer(dist[0]);
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            
            int cv = cur.vertex;
            int ccnt = cur.cnt;
            int ccost = cur.cost;
            
            if(dist[cv].cnt < ccnt || (dist[cv].cnt == ccnt && dist[cv].cost < ccost))
                continue;
            
            for(int[] edge : graph[cv]) {
                int nv = edge[0];
                int ncnt = (companies[cv] == companies[nv]) ? ccnt : ccnt + 1;
                int ncost = ccost + edge[1];
                
                if(ncnt < dist[nv].cnt || (ncnt == dist[nv].cnt && ncost < dist[nv].cost)) {
                    dist[nv].cnt = ncnt;
                    dist[nv].cost = ncost;
                    pq.offer(dist[nv]);
                }
            }
        }
        
        System.out.println(dist[E]);
    }
    
    private static class Node implements Comparable<Node> {
        public int vertex;
        public int cnt;
        public int cost;
        
        public Node(int vertex, int cnt, int cost) {
            this.vertex = vertex;
            this.cnt = cnt;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            int ret = Integer.compare(this.cnt, other.cnt);
            
            if(ret == 0)
                ret = Integer.compare(this.cost, other.cost);
            
            return ret;
        }
        
        @Override
        public String toString() {
            return this.cnt + " " + this.cost;
        }
    }
}    //    Main-class-end
```