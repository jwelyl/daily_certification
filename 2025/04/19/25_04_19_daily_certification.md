# 25_04_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23040 누텔라 트리 (Easy)

[23040번: 누텔라 트리 (Easy)](http://boj.ma/23040/t)

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
    
    private static int N;                    //    정점 개수
    private static List<Integer>[] tree;     
    private static boolean[] visited;
    
    private static char[] colors;
    
    private static int[] parents;
    private static int[] cnts;
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        tree = new ArrayList[N + 1];
        visited = new boolean[N + 1];
        
        parents = new int[N + 1];
        cnts = new int[N + 1];
        
        for(int v = 0; v <= N; v++) {
            tree[v] = new ArrayList<>();
            parents[v] = v;
            cnts[v] = 1;
        }
        
        for(int e = 0; e < N - 1; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            tree[v1].add(v2);
            tree[v2].add(v1);
        }
        
        colors = br.readLine().toCharArray();
        
        for(int v = 1; v <= N; v++) {
            if(colors[v - 1] == 'R')
                dfs(v);
        }
        
        for(int v = 1; v <= N; v++) {
            if(colors[v - 1] == 'B') {
                for(int nv : tree[v]) {
                     if(colors[nv - 1] == 'R') {
                         int nvDs = find(nv);
                         
                         answer += cnts[nvDs];
                     }       
                }
            }
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static void dfs(int v) {
        visited[v] = true;
        
        for(int nv : tree[v]) {
            if(colors[nv - 1] == 'R' && !visited[nv]) {
                union(v, nv);
                dfs(nv);
            }
        }
    }
    
    private static int find(int v) {
        if(v == parents[v])
            return v;
        
        return parents[v] = find(parents[v]);
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        if(v1 != v2) {
            parents[v2] = v1;
            cnts[v1] += cnts[v2];
            cnts[v2] = 0;
        }
    }
}    //    Main-class-end
```