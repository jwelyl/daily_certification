# 25_02_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16947 서울 지하철 2호선

[16947번: 서울 지하철 2호선](http://boj.ma/16947/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
import java.util.Arrays;

public class Main {
    private static final int UNVISITED = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static List<Integer>[] graph;
    private static boolean[] visited;
    private static int[] parents;
    private static int[] dists;
    private static final Queue<Integer> queue = new LinkedList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        graph = new ArrayList[N + 1];
        visited = new boolean[N + 1];
        parents = new int[N + 1];
        dists = new int[N + 1];
        for(int v = 0; v <= N; v++) {
            graph[v] = new ArrayList<>();   
            dists[v] = UNVISITED;
        }
        
        for(int e = 1; e <= N; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            graph[v1].add(v2);
            graph[v2].add(v1);
        }
        
        dfs(1, 0);
        
        bfs();
    }    //    main-end
    
    private static void bfs() {
        while(!queue.isEmpty()) {
            int cur = queue.poll();
            
            for(int next : graph[cur]) {
                if(dists[next] == UNVISITED) {
                    dists[next] = dists[cur] + 1;
                    queue.offer(next);
                }
            }
        }
        
        for(int v = 1; v <= N; v++)
            sb.append(dists[v]).append(" ");
        
        System.out.println(sb);
    }
    
    private static void dfs(int vertex, int parent) {
        visited[vertex] = true;
        parents[vertex] = parent;
        
        for(int next : graph[vertex]) {
           if(!visited[next])
               dfs(next, vertex);
            else if(parents[vertex] != next) {    //    사이클 발견
                dists[next] = 0;
                findCycle(vertex);
            }
        }
    }
    
    private static void findCycle(int vertex) {
        queue.offer(vertex);
        
        if(dists[vertex] != 0) {
            dists[vertex] = 0;
            findCycle(parents[vertex]);
        }
    }
}    //    Main-class-end
```