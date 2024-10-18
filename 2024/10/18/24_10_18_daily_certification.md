# 24_10_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15591 **MooTube (Silver)**

[](https://www.acmicpc.net/problem/15591)

```java
import java.io.IOException;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int Q;
    
    private static List<Edge>[] tree;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        Q = Integer.parseInt(st.nextToken());
        
        tree = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            tree[v] = new ArrayList<>();
        
        for(int m = 0; m < N - 1; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            tree[v1].add(new Edge(v2, c));
            tree[v2].add(new Edge(v1, c));
        }
        
        for(int q = 0; q < Q; q++) {
            st = new StringTokenizer(br.readLine());
            int k = Integer.parseInt(st.nextToken());
            int v = Integer.parseInt(st.nextToken());
            
            sb.append(dfs(v, k, new boolean[N + 1])).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static int dfs(int v, int k, boolean[] visited) {
        int res = 0;
        visited[v] = true;
        
        for(Edge edge : tree[v]) {
            int next = edge.vertex;
            int cost = edge.cost;
            
            if(!visited[next] && cost >= k) {
                res++;
                res += dfs(next, k, visited);
            }
        }
        
        return res;
    }
    
    private static class Edge {
        public int vertex;
        public int cost;
        
        public Edge(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
    }
}    //    Main-class-end
```

### BOJ 10775 공항

[](https://www.acmicpc.net/problem/10775)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final int CLOSED = 0;    //    공항 폐쇄
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int G;    //    공항 게이트 수
    private static int P;    //    비행기 수
    private static int[] parents;
    
    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        G = Integer.parseInt(br.readLine());
        P = Integer.parseInt(br.readLine());
        
        parents = new int[G + 1];
        for(int v = 1; v <= G; v++)
            parents[v] = v;
        
        for(int p = 1; p <= P; p++) {
            int g = Integer.parseInt(br.readLine());    //   p번째 비행기가 도킹하려는 게이트 번호
            
            int gate = findDS(g);	//	실제로 도킹할 수 있는 게이트 번호
            
            if(gate == CLOSED)	//	도킹할 수 없다면 공항 폐쇄
            	break;
            
            ans++;	//	도킹할 수 있는 비행기 1개 증가
            unionDS(gate, gate - 1);
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static int findDS(int v) {
        if(v == parents[v])
            return v;
        
        return parents[v] = findDS(parents[v]);
    }
    
    private static void unionDS(int v1, int v2) {
        int ds1 = findDS(v1);
        int ds2 = findDS(v2);
        
        parents[ds1] = ds2;
    }
}    //    Main-class-end
```