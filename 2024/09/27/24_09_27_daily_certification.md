# 24_09_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12004 Closing the Farm (Silver)

[](https://www.acmicpc.net/problem/12004)

```java
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
    private static int M;
    
    private static List<Edge>[] graph;
    
    private static boolean[] deleted;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
            
        graph = new ArrayList[N + 1];
        for(int v = 1; v <= N; v++)
            graph[v] = new ArrayList<>();
        deleted = new boolean[N + 1];
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            Edge e1 = new Edge(v2);
            Edge e2 = new Edge(v1);
            
            e1.twin = e2;
            e2.twin = e1;
            
            graph[v1].add(e1);
            graph[v2].add(e2);
        }
        
        for(int q = 0; q < N; q++) {
            int remain = N - q;
            
            for(int v = 1; v <= N; v++) {
                if(!deleted[v]) {
                    if(dfs(v, new boolean[N + 1]) == remain)
                        sb.append("YES\n");
                    else
                        sb.append("NO\n");
                    
                    break;
                }
            }
            
            int deletion = Integer.parseInt(br.readLine());
            for(Edge edge : graph[deletion]) {
                edge.alive = false;
                edge.twin.alive = false;
            }
            
            deleted[deletion] = true;
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static int dfs(int v, boolean[] visited) {
        int ret = 1;
        visited[v] = true;
        
        for(Edge edge : graph[v]) {
            if(!edge.alive || visited[edge.vertex])
                continue;
            
            ret += dfs(edge.vertex, visited);   
        } 
        
        return ret;
    }
    
    private static class Edge {
        public int vertex;
        public Edge twin;
        public boolean alive;
        
        public Edge(int vertex) {
            this.vertex = vertex;
            this.twin = null;
            this.alive = true;
        }
    }
}    //    Main-class-end
```

### BOJ 17299 오등큰수

[](https://www.acmicpc.net/problem/17299)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 1_000_001;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static final int[] f = new int[MAX];
    private static int[] sequence;
    private static int[] ngfs;
    
    private static final Stack<Integer> stack = new Stack<>();    //    오른쪽에 존재하는 수들 저장
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        sequence = new int[N];
        ngfs = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int num = Integer.parseInt(st.nextToken());
            sequence[i] = num;
            f[num]++;
        }
        
        for(int i = N - 1; i >= 0; i--) {    //    뒤에서부터 확인
            int num = sequence[i];    //    i번째 수
            int cnt = f[num];         //    i번째 수의 등장횟수
            int ans = -1;    //    i번째 수 num의 오등큰수

            while(!stack.isEmpty()) {
                int right = stack.peek();
                if(f[right] > cnt) {
                	ans = right;
                	break;
                }
            	
            	stack.pop();
            }
            
            stack.push(num);
            ngfs[i] = ans;
        }
        
        for(int i = 0; i < N; i++)
            sb.append(ngfs[i]).append(" ");
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```