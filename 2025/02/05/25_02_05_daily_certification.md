# 25_02_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14990 Crowd Control

[14990번: Crowd Control](http://boj.ma/14990/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Set;
import java.util.HashSet;
import java.util.TreeSet;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    
    private static int[][] edges;
    private static boolean[] edgeUsed;
    
    private static int[] resPrevs;
    private static int maxValue = 0;
    
    private static int max = 0;
    
    private static final TreeSet<Integer> allEdges = new TreeSet<>();     //    경로에 포함된 간선 + 인접한 모든 간선
    private static final Set<Integer> paths = new HashSet<>();            //    경로에 포함된 간선
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N];
        for(int v = 0; v < N; v++)
            graph[v] = new ArrayList<>();
        
        edges = new int[M][3];
        edgeUsed = new boolean[M];
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            max = Math.max(max, c);
            
            edges[e][0] = v1;
            edges[e][1] = v2;
            edges[e][2] = c;
            
            graph[v1].add(e);
            graph[v2].add(e);
        }
        
        parametricSearch();
    }    //    main-end
    
    private static void parametricSearch() {
        int start = 1;
        int end = max;
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    mid 이상의 간선만 지난다고 가정
            int[] prevs = new int[N];
            
            if(dfs(0, -1, mid, new boolean[N], prevs)) {    //    mid 이상의 간선으로 지날 수 있다고 가정하면
                resPrevs = prevs;
                maxValue = mid;
                start = mid + 1;    //    더 큰 값의 간선만으로 갈 수 있나 체크
            }
            else
                end = mid - 1;
        }
        
        int cur = N - 1;
        while(true) {
            for(int eNum : graph[cur])
                allEdges.add(eNum);
            
            int edge = resPrevs[cur];
            
            if(edge == -1)
                break;
            
            paths.add(edge);
            cur = edges[edge][0] == cur ? edges[edge][1] : edges[edge][0]; 
        }
        
        if(allEdges.size() == paths.size())
            sb.append("none\n");
        else {
            for(int edge : allEdges) {
                if(!paths.contains(edge))
                    sb.append(edge).append(" ");
            }
            
            sb.append("\n");
        }
        
        System.out.print(sb);
    }
    
    private static boolean dfs(int vertex, int prev, int limit, boolean[] visited, int[] prevs) {
        visited[vertex] = true;
        prevs[vertex] = prev;
        
        if(vertex == N - 1)    //    N - 1에 도착한 경우
            return true;
        
        for(int e : graph[vertex]) {
            int next = edges[e][0] == vertex ? edges[e][1] : edges[e][0];    //    다음 정점
            int cost = edges[e][2];
            
            if(cost >= limit && !visited[next]) {    //    지날 수 있으면
                if(dfs(next, e, limit, visited, prevs))
                    return true;
            }
        }
        
        return false;
    }
}    //    Main-class-end
```