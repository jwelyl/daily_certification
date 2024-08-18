# 24_08_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9370 **미확인 도착지**

[](https://www.acmicpc.net/problem/9370)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int NONE = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int TC;    //    테스트 케이스 개수
    private static int N;     //    정점 개수
    private static int M;     //    간선 개수
    private static int T;     //    목적지 후보 개수
    
    private static int S;     //    출발 정점
    private static int G;
    private static int H;     //    지나간 것으로 생각되는 간선의 두 정점
    
    private static List<Node>[] graph;    //    그래프
    private static int[] distS;           //    S로부터의 최단 거리
    private static int[] distG;			  //	G로부터의 최단 거리
    private static int[] distH;			  //	H로부터의 최단 거리
    private static int[] arrivals;        //    목적지 후보
    
    private static int ghCost;			  //	(G, H) 간선 비용
    
    public static void main(String[] args) throws IOException {
        TC = Integer.parseInt(br.readLine());
        
        for(int tc = 0; tc < TC; tc++) {
            st = new StringTokenizer(br.readLine());
            N = Integer.parseInt(st.nextToken());
            M = Integer.parseInt(st.nextToken());
            T = Integer.parseInt(st.nextToken());
            
            graph = new ArrayList[N + 1];
            for(int i = 0; i <= N; i++)
                graph[i] = new ArrayList<>();
            
            distS = new int[N + 1];
            distG = new int[N + 1];
            distH = new int[N + 1];
            arrivals = new int[T];
            
            st = new StringTokenizer(br.readLine());
            S = Integer.parseInt(st.nextToken());
            G = Integer.parseInt(st.nextToken());
            H = Integer.parseInt(st.nextToken());
            
            for(int m = 0; m < M; m++) {
                st = new StringTokenizer(br.readLine());
                int v1 = Integer.parseInt(st.nextToken());
                int v2 = Integer.parseInt(st.nextToken());
                int c = Integer.parseInt(st.nextToken());
                
                graph[v1].add(new Node(v2, c));
                graph[v2].add(new Node(v1, c));
                
                if(v1 == G && v2 == H || v1 == H && v2 ==G)
                	ghCost = c;
            }
            
            for(int t = 0; t < T; t++)
                arrivals[t] = Integer.parseInt(br.readLine());
            
            Arrays.sort(arrivals);
            
            dijkstra(distS, S);    //    S로부터 최단 거리 구하기
            dijkstra(distG, G);	   //	 G로부터 최단 거리 구하기
            dijkstra(distH, H);	   //    H로부터 최단 거리 구하기
            
            for(int t = 0; t <  T; t++) {
            	int distST = distS[arrivals[t]];	//	S부터 목적지 T까지의 최단 거리
            	int distSG = distS[G];				//	S부터 G까지의 최단 거리
            	int distSH = distS[H];				//	S부터 H까지의 최단 거리
            	int distGT = distG[arrivals[t]];	//	G부터 목적지 T까지의 최단 거리
            	int distHT = distH[arrivals[t]];	//	H부터 목적지 T까지의 최단 거리
            	
            	if(distST == NONE)
            		continue;
            	
            	if(distST == distSG + ghCost + distHT)
            		sb.append(arrivals[t]).append(" ");
            	else if(distST == distSH + ghCost + distGT)
            		sb.append(arrivals[t]).append(" ");
            }
            
            sb.append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dijkstra(int[] dist, int start) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        Arrays.fill(dist, NONE);
        dist[start] = 0;
        
        pq.offer(new Node(start, dist[start]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;

            if(dist[cv] < cc)
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                int nc = cc + next.cost;
                
                if(nc < dist[nv]) {    //    cv를 거쳐서 nv로 가는게 더 짧을 경우
                	dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
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