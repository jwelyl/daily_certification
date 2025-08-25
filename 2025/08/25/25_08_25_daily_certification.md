# 25_08_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1854 K번째 최단경로 찾기

[1854번: K번째 최단경로 찾기](http://boj.ma/1854/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.Collections;

public class Main {
    private static final int INF = 250_000 * 1_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    private static int K;
    
    private static List<int[]>[] graph;
    
    //    distPQ[v] : 정점 1에서 정점 v까지 K번째 최단거리, K-1번째 최단거리, ... , 1번째 최단거리 저장
    private static PriorityQueue<Integer>[] distPQ;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        distPQ = new PriorityQueue[N + 1];
        for(int v = 1; v <= N; v++) {
            graph[v] = new ArrayList<>();
            distPQ[v] = new PriorityQueue<>(Collections.reverseOrder());
        }
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new int[] {v2, c});
        }
        
        dijkstra();
        
        for(int v = 1; v <= N; v++) {
            if(distPQ[v].size() < K)    //    정점 1에서 정점 v까지 K번째 최단거리가 없을 경우
                sb.append("-1\n");
            else                        //    정점 1에서 정점 v까지 K번째 최단거리가 존재할 경우
                sb.append(distPQ[v].poll()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        distPQ[1].offer(0);    //    정점 1에서 정점 1까지 1번째 최단거리는 0임
        pq.offer(new Node(1, 0));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;
            
            for(int[] edge : graph[cv]) {
                int nv = edge[0];
                int nc = cc + edge[1];
                Node next = new Node(nv, nc);
                
                if(distPQ[nv].size() < K) {    //    아직 정점 1에서 정점 nv까지 K개의 최단거리가 없을 경우
                    distPQ[nv].offer(nc);      //    최단거리 추가
                    pq.offer(next);
                }
                else if(distPQ[nv].size() == K) {    //    이미 정점 1에서 정점 nv까지 K개의 최단거리가 있을 경우
                    int kth = distPQ[nv].peek();     //    현재 정점 1에서 정점 nv까지 K개의 최단거리
                    
                    if(nc < kth) {   //    K번째 최단거리가 갱신되는 경우
                        distPQ[nv].poll();        //    기존 K번째 최단거리 버리기
                        distPQ[nv].offer(nc);
                        pq.offer(next);
                    }
                }
            }
        }
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
}    //    Main-class-end
```