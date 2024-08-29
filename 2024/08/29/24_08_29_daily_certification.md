# 24_08_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5590 **船旅**

[](https://www.acmicpc.net/problem/5590)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;        //    정점 개수
    private static int K;        //    쿼리 개수
    
    private static List<Node>[] graph;
    private static long[] dist;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        dist = new long[N + 1];
        
        for(int q = 0; q < K; q++) {
            st = new StringTokenizer(br.readLine());
            int cmd = Integer.parseInt(st.nextToken());
            int v1;
            int v2;
            int cost;
            
            if(cmd == 0) {
            	v1 = Integer.parseInt(st.nextToken());
                v2 = Integer.parseInt(st.nextToken());
                
                dijkstra(v1);
                
                sb.append(dist[v2] == Integer.MAX_VALUE ? -1 : dist[v2]).append("\n");
            }
            else {
	            v1 = Integer.parseInt(st.nextToken());
	            v2 = Integer.parseInt(st.nextToken());
	            cost = Integer.parseInt(st.nextToken());
	            
	            graph[v1].add(new Node(v2, cost));
	            graph[v2].add(new Node(v1, cost));
            }
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dijkstra(int start) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        
        Arrays.fill(dist, Integer.MAX_VALUE);
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
        public long cost;      //    비용
        
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