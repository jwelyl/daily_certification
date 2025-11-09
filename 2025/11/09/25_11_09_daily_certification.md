# 25_11_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22868 산책 (small)

[22868번: 산책 (small)](http://boj.ma/22868/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    private static int S;    //    출발 정점
    private static int E;    //    도착 정점
    
    private static List<Integer>[] graph;
    private static boolean[] visited;
    private static int[] prevs;       //    prevs[v] : S->E로 최단거리/사전순으로 빠르게 이동할 때 v의 다음 정점
    private static int answer = 0;    //    S->E까지 최단거리/사전순으로 빠른 순으로 이동 후 E->S까지 이동한 거리 합
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        visited = new boolean[N + 1];
        prevs = new int[N + 1];
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            graph[v1].add(v2);
            graph[v2].add(v1);
        }
        
        for(int v = 1; v <= N; v++)
            Collections.sort(graph[v]);
        
        st = new StringTokenizer(br.readLine());
        S = Integer.parseInt(st.nextToken());
        E = Integer.parseInt(st.nextToken());
        
        answer = bfs(S, E);
        setVisited();
        answer += bfs(E, S);
        
        System.out.println(answer);
    }    //    main-end
    
    private static void setVisited() {
        Arrays.fill(visited, false);
        
        int cur = E;
        while(true) {
            cur = prevs[cur];
            
            if(cur == S)
                break;
            
            visited[cur] = true;
        }
    }
    
    private static int bfs(int from, int to) {
        Queue<Integer> queue = new ArrayDeque<>();
        int res = 0;
        
        visited[from] = true;
        queue.offer(from);
        
        while(!queue.isEmpty()) {
            int size = queue.size();
            
            for(int i = 0; i < size; i++) {
                int cur = queue.poll();
            
                for(int next : graph[cur]) {
                    if(!visited[next]) {
                        visited[next] = true;
                        prevs[next] = cur;
                    
                        if(next == to)
                            return res + 1;
                    
                        queue.offer(next);
                    }
                }
            }
            
            res++;
        }
        
        return -1;
    }
}    //    Main-class-end
```