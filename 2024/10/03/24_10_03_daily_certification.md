# 24_10_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24512 Bottleneck Travelling Salesman Problem (Small)

[](https://www.acmicpc.net/problem/24512)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final int NONE = 0;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    
    private static int[][] graph;    //    graph[i][j] : 정점 i에서 정점 j로 가는 간선 비용 (간선 없으면 NONE)
    
    private static int minBottleneck = Integer.MAX_VALUE;    //    TSP 중 bottleneck이 가장 작은 TSP의 bottleneck
    private static int[] tspPath = null;    //    그때 TSP 경로
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new int[N + 1][N + 1];
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1][v2] = c;
        }
        
        for(int start = 1; start <= N; start++) {
            boolean[] visited = new boolean[N + 1];
            int[] path = new int[N];
            tsp(start, start, 1, 0, visited, path);
        }
        
        if(minBottleneck == Integer.MAX_VALUE)    //    TSP가 없을 경우
            sb.append(-1);
        else {
            sb.append(minBottleneck).append("\n");
            for(int i = 0; i < N; i++)
                sb.append(tspPath[i]).append(" ");
        }
        
        System.out.println(sb);
    }    //    main-end
    
    private static void tsp(int start, int cur, int nth, int bottleneck, boolean[] visited, int[] path) {
        path[nth - 1] = cur;
        visited[cur] = true;
        
        if(nth == N) {    //    출발지 제외하고 다 돌았을 경우
            if(graph[cur][start] != NONE) {    //    TSP 존재
                bottleneck = Math.max(bottleneck, graph[cur][start]);
                
                if(bottleneck < minBottleneck) {
                    minBottleneck = bottleneck;
                    
                    tspPath = Arrays.copyOf(path, N);
                }
            }
            
            visited[cur] = false;
            return;
        }
        
        for(int next = 1; next <= N; next++) {
            if(graph[cur][next] != NONE && !visited[next] && graph[cur][next] <= minBottleneck)
                tsp(start, next, nth + 1, Math.max(bottleneck, graph[cur][next]), visited, path);
        }
        
        visited[cur] = false;
    }
}    //    Main-class-end
```

### BOJ 1300 K번째 수

[](https://www.acmicpc.net/problem/1300)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    private static int K;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        K = Integer.parseInt(br.readLine());
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static long parametricSearch() {
        long start = 1L;
        long end = (N + 0L) * N;
        long res = 0L;
        
        while(start <= end) {
            long mid = (start + end) / 2;
            
            if(cntOf(mid) < K)      //    mid 이하의 수가 K개 미만일 경우, mid는 K번째 수가 되기에 너무 작음
                start = mid + 1;    //    mid를 더 키워야 함
            else {    //    mid 이하의 수가 K개 이상일 경우
                res = mid;        //    일단 mid 저장
                end = mid - 1;    //    mid를 줄여서 mid 이하의 수가 딱 K개인 경우가 없는지 찾아야 함, 없을 경우, 이전에 구한 mid가 정답
            }
        }
        
        return res;
    }
    
    private static long cntOf(long num) {    //    정렬된 N * N 배열에서 num 이하 수의 개수
        long cnt = 0;
        
        for(int row = 1; row <= N; row++)
            cnt += Math.min(num / row, N);
        
        return cnt;
    }
}    //    Main-class-end
```

### BOJ 23247 Ten

[](https://www.acmicpc.net/problem/23247)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[][] prefixSum;
    
    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        prefixSum = new int[N + 1][M + 1];
        
        for(int r = 1; r <= N; r++) {
            st = new StringTokenizer(br.readLine());
            
            for(int c = 1; c <= M; c++)
                prefixSum[r][c] = prefixSum[r - 1][c] + prefixSum[r][c - 1] - prefixSum[r - 1][c - 1] + Integer.parseInt(st.nextToken());
        }
        
        for(int r1 = 1; r1 <= N; r1++) {
            for(int c1 = 1; c1 <= M; c1++) {
                for(int r2 = r1; r2 <= Math.min(r1 + 9, N); r2++) {
                    for(int c2 = c1; c2 <= Math.min(c1 + 9, M); c2++) {
                        if(partialSum(r1, c1, r2, c2) == 10)
                            ans++;
                    }
                }
            }
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static int partialSum(int y1, int x1, int y2, int x2) {
        return prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1];
    }
}    //    Main-class-end
```