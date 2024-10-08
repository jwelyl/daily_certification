# 24_10_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1368 물대기

[](https://www.acmicpc.net/problem/1368)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    논 개수
    
    private static final List<Edge> edges = new ArrayList<>();
    private static int[] parents;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        parents = new int[N + 1];
        for(int i = 1; i <= N; i++)
            parents[i] = i;
        
        for(int v = 1; v <= N; v++) {
            int cost = Integer.parseInt(br.readLine());
            edges.add(new Edge(0, v, cost));
        }
        
        for(int v1 = 1; v1 <= N; v1++) {
            st = new StringTokenizer(br.readLine());
            for(int v2 = 1; v2 <= N; v2++) {
                int cost = Integer.parseInt(st.nextToken());
                
                if(v1 < v2)
                    edges.add(new Edge(v1, v2, cost));
            }
        }
        
        System.out.println(kruskal());
    }    //    main-end
    
    private static int kruskal() {
        int mstCost = 0;
        int edgeCnt = 0;
        
        Collections.sort(edges);
        
        for(Edge edge : edges) {
            int v1 = edge.v1;
            int v2 = edge.v2;
            int cost = edge.cost;
            
            int v1DS = findDS(v1);
            int v2DS = findDS(v2);
            
            if(v1DS != v2DS) {
                union(v1DS, v2DS);
                mstCost += cost;
                edgeCnt++;
            }
            
            if(edgeCnt == N)
                break;
        }
        
        return mstCost;
    }
    
    private static int findDS(int v) {
        if(parents[v] == v)
            return v;
        
        return parents[v] = findDS(parents[v]);
    }
    
    private static void union(int v1, int v2) {
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

### BOJ 3109 빵집

[](https://www.acmicpc.net/problem/3109)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int R;
    private static int C;
    
    private static char[][] map;
    
    private static int ans = 0;    //    (r1, 0)에서 (r2, C - 1)까지 갈 수 있는 경로 수 (0 <= r1, r2 < R)
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        R = Integer.parseInt(st.nextToken());
        C = Integer.parseInt(st.nextToken());
        map = new char[R][C];
        
        for(int r = 0; r < R; r++)
            map[r] = br.readLine().toCharArray();
        
        for(int r = 0; r < R; r++) {
            if(dfs(r, 0))    //    (r, 0)에서 출발해서 C - 1 열에 도달 가능할 경우
                ans++;
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static boolean dfs(int y, int x) {
        map[y][x] = 'x';    //    방문 처리
        
        if(x == C - 1)    //    C - 1 열에 도달한 경우
            return true;
        
        for(int d = 0; d < 3; d++) {
            int ny = y + dy[d];
            int nx = x + dx[d];    //    다음 칸
            
            if(canGo(ny, nx)) {    //    다음 칸에 갈 수 있을 경우
                if(dfs(ny, nx))
                    return true;
            }
        }
        
        //    (y, x)에서는 C - 1 열에 도달할 수 없음
        return false;
    }
    
    private static final int[] dy = {-1, 0, 1};
    private static final int[] dx = {1, 1, 1};
    
    private static boolean canGo(int y, int x) {
        return (0 <= y && y < R) && (0 <= x && x < C) && map[y][x] != 'x';
    }
}    //    Main-class-end
```