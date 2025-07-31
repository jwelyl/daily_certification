# 25_07_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24526 전화 돌리기

[24526번: 전화 돌리기](http://boj.ma/24526/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 수
    private static int M;    //    간선 수
    
    private static List<Integer>[] graph;    //    역방향 그래프
    private static int[] indegrees;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        indegrees = new int[N + 1];
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int u = Integer.parseInt(st.nextToken());
            int v = Integer.parseInt(st.nextToken());
            
            graph[v].add(u);
            indegrees[u]++;
        }

        topologicalSort();
        
        System.out.println(answer);
    }    //    main-end

    private static void topologicalSort() {
        Queue<Integer> queue = new LinkedList<>();
        
        for(int v = 1; v <= N; v++) {
            if(indegrees[v] == 0) {
                answer++;
                queue.offer(v);
            }
        }
        
        while(!queue.isEmpty()) {
            int cur = queue.poll();
            
            for(int next : graph[cur]) {
                indegrees[next]--;
                if(indegrees[next] == 0) {
                    answer++;
                    queue.offer(next);
                }
            }
        }
    }
}    //    Main-class-end
```