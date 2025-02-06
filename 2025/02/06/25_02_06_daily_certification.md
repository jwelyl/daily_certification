# 25_02_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16724 피리 부는 사나이

[16724번: 피리 부는 사나이](http://boj.ma/16724/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    private static boolean[] visited;
    private static int[] parents;
    private static boolean[] finished;
    
    private static int answer = 0;    //    사이클 갯수
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N * M];
        for(int v = 0; v < N * M; v++)
            graph[v] = new ArrayList<>();
        
        visited = new boolean[N * M];
        parents = new int[N * M];
        finished = new boolean[N * M];
        
        for(int y = 0; y < N; y++) {
            String input = br.readLine();
            for(int x = 0; x < M; x++) {
                char dir = input.charAt(x);
                int curV = M * y + x;
                
                int ny = (dir == 'L' || dir == 'R') ? y : (dir == 'U' ? y - 1 : y + 1);
                int nx = (dir == 'U' || dir == 'D') ? x : (dir == 'L' ? x - 1 : x + 1);
                int nextV = M * ny + nx;
                
                graph[curV].add(nextV);
            }
        }
        
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < M; x++) {
                int vertex = M * y + x;
                
                if(!visited[vertex])
                	dfs(vertex);
            }
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static void dfs(int cur) {
        visited[cur] = true;
        
        for(int next : graph[cur]) {
            if(!visited[next]) {
            	parents[next] = cur;
            	dfs(next);
            }
            else if(!finished[next])
            	makeCycle(cur, next);
        }
        
        finished[cur] = true;
    }
    
    private static void makeCycle(int cur, int next) {
    	if(cur == next) {
    		answer++;
    		return;
    	}
    	
    	int prev = parents[cur];
    	makeCycle(prev, next);
    }
}    //    Main-class-end
```