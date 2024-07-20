# 24_07_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6248 Bronze Cow Party

[](https://www.acmicpc.net/problem/6248)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    
    private static int N;
    private static int M;
    private static int X;
    
    private static List<Node>[] graph;
    
    private static int[] dist;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        N = Integer.parseInt(tokens.nextToken());
        M = Integer.parseInt(tokens.nextToken());
        X = Integer.parseInt(tokens.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 1; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        dist = new int[N + 1];
        
        for(int i = 0; i < M; i++) {
            tokens = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(tokens.nextToken());
            int v2 = Integer.parseInt(tokens.nextToken());
            int c = Integer.parseInt(tokens.nextToken());
            
            graph[v1].add(new Node(v2, c));
            graph[v2].add(new Node(v1, c));
        }
        
        System.out.println(dijkstra());
    }   //    main-end
    
    private static int dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[X] = 0;
        
        pq.offer(new Node(X, dist[X]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;
            
            if(dist[cv] < cc)
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                int nc = cc + next.cost;
                
                if(dist[nv] > nc) {
                    dist[nv] = nc;
                    pq.offer(new Node(nv, nc));
                }
            }
        }
        
        int maxDist = 0;
        for(int i = 1; i <= N; i++)
            maxDist = Math.max(maxDist, dist[i]);
        
        return maxDist * 2;
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
}   //    Main-class-end
```