# 25_09_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23743 방탈출

[23743번: 방탈출](http://boj.ma/23743/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final int EXIT = 0;    //    비상 탈출구
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    방 개수
    private static int M;    //    워프 개수
    
    private static int[] parents;
    
    private static final List<Edge> edgeList = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        parents = new int[N + 1];
        for(int v = 0; v <= N; v++)
            parents[v] = v;
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int cost = Integer.parseInt(st.nextToken());
            
            edgeList.add(new Edge(v1, v2, cost));
        }
        
        st = new StringTokenizer(br.readLine());
        for(int v = 1; v <= N; v++)
            edgeList.add(new Edge(EXIT, v, Integer.parseInt(st.nextToken())));
        
        System.out.println(kruskal());
    }    //    main-end
    
    private static int kruskal() {
        int mstCost = 0;
        int edgeCnt = 0;
        
        Collections.sort(edgeList);
        
        for(Edge edge : edgeList) {
            int v1 = edge.v1;
            int v2 = edge.v2;
            int cost = edge.cost;
            
            if(find(v1) != find(v2)) {
                union(v1, v2);
                mstCost += cost;
                edgeCnt++;
                
                if(edgeCnt == N)
                    break;
            }
        }
        
        return mstCost;
    }
    
    private static int find(int v) {
        return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        if(v1 != v2)
            parents[v2] = v1;
    }
    
    private static class Edge implements Comparable<Edge> {
        public int v1;
        public int v2;
        public int cost;
        
        public Edge(int v1, int v2, int cost) {
            this.v1 = v1;
            this.v2 = v2;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Edge other) {
             return Integer.compare(this.cost, other.cost);   
        }
    }
}    //    Main-class-end
```