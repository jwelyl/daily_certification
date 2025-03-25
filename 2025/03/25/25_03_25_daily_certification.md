# 25_03_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1800 인터넷 설치

[1800번: 인터넷 설치](http://boj.ma/1800/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.PriorityQueue;
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;

public class Main { 
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int P;
    private static int K;
    
    private static List<int[]>[] graph;
    
    private static int maxCost = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        P = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int p = 0; p < P; p++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            maxCost = Math.max(maxCost, c);
            
            graph[v1].add(new int[] {v2, c});
            graph[v2].add(new int[] {v1, c});
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = maxCost;
        int res = -1;
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    공짜 K개를 제외한 나머지 전선 중 가장 비쌀 금액 mid
            
            if(dijkstra(mid) <= K) {    //    K개를 제외한 전선 중 가장 비싼 금액이 mid여도 충분할 경우
                res = mid;              //    일단 mid 저장
                end = mid - 1;          //    더 적은 가격 가능할지 확인
            }
            else
                start = mid + 1;
        }
        
        return res;
    }
    
    private static int dijkstra(int mid) {
        PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
        int[] dist = new int[N + 1];
        Arrays.fill(dist, 10_001);
        dist[1] = 0;
        
        pq.offer(new int[] {1, dist[1]});
        
        while(!pq.isEmpty()) {
            int[] cur = pq.poll();
            int cv = cur[0];
            int cc = cur[1];
            
            if(dist[cv] < cc)
                continue;
            
            for(int[] edge : graph[cv]) {
                int nv = edge[0];
                int nc = cc + (edge[1] > mid ? 1 : 0);
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new int[] {nv, dist[nv]});
                }
            }
        }
        
        return dist[N];
    }
}    //    Main-class-end
```