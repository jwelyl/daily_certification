# 25_09_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5214 환승

[5214번: 환승](http://boj.ma/5214/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.List;
import java.util.ArrayList;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    역 개수
    private static int K;    //    하이퍼튜브 하나에 연결된 역 개수
    private static int M;    //    하이퍼튜브 개수
    
    private static List<Integer>[] graph;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        if(N == 1) {
            System.out.println(1);
            return;
        }
            
        //    1 ~ N : 역
        //    N + 1 ~ (N + M) : 하이퍼튜브
        graph = new ArrayList[N + M + 1];
        for(int v = 0; v <= N + M; v++)
            graph[v] = new ArrayList<>();
        
        for(int tube = N + 1; tube <= N + M; tube++) {
            st = new StringTokenizer(br.readLine());
            for(int k = 0; k < K; k++) {
                int v = Integer.parseInt(st.nextToken());    //    tube와 연결된 역 번호
                
                graph[tube].add(v);
                graph[v].add(tube);
            }
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<int[]> queue = new LinkedList<>();
        boolean[] visited = new boolean[N + M + 1];
        
        visited[1] = true;
        queue.offer(new int[] {1, 1});
        
        while(!queue.isEmpty()) {
            int size = queue.size();
            
            for(int i = 0; i < size; i++) {
                int[] cur = queue.poll();
                int cv = cur[0];
                int cc = cur[1];
                
                for(int nv : graph[cv]) {
                    int nc = cc;
                    
                    if(N + 1 <= cv && cv <= N + M)    //    cv가 튜브일 경우
                        nc++;    //    튜브에 연결된 다른 역 가기 위해 지난 역 1 증가 (그 다른 역)
                    
                    if(nv == N)
                        return nc;
                    
                    if(!visited[nv]) {
                        visited[nv] = true;
                        queue.offer(new int[] {nv, nc});
                    }
                }
            }
        }
        
        return -1;    //    N번 역에 도달하지 못할 경우
    }
}    //    Main-class-end
```