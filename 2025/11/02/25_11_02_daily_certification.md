# 25_11_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26009 험난한 등굣길

[26009번: 험난한 등굣길](http://boj.ma/26009/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final int INF = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static boolean[][] visited;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        visited = new boolean[N][M];
        
        K = Integer.parseInt(br.readLine());
        
        for(int k = 1; k <= K; k++) {
            st = new StringTokenizer(br.readLine());
            int y = Integer.parseInt(st.nextToken()) - 1;
            int x = Integer.parseInt(st.nextToken()) - 1;
            int d = Integer.parseInt(st.nextToken());
            
            put(y, x, d);
        }
        
        bfs();
    }    //    main-end
    
    private static void bfs() {
        Queue<int[]> queue = new ArrayDeque<>();
        int res = INF;
        
        visited[0][0] = true;
        queue.offer(new int[] {0, 0, 0});
        
        OUTER:
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];
            int cd = cur[2];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                
                if(isIn(ny, nx) && !visited[ny][nx]) {
                    if(ny == N - 1 && nx == M - 1) {
                        res = cd + 1;
                        break OUTER;
                    }
                    
                    visited[ny][nx] = true;
                    queue.offer(new int[] {ny, nx, cd + 1});
                }
            }
        }
        
        if(res == INF)
            System.out.println("NO");
        else {
            System.out.println("YES");
            System.out.println(res);
        }
    }
    
    private static void put(int y, int x, int d) {
        int diff = y - x;
        int sum = y + x;
        for(int yy = y; yy <= y + d; yy++) {
            int xx = yy - d - diff;
            
            if(isIn(yy, xx))
                visited[yy][xx] = true;
            
            xx = -yy + sum + d;
            if(isIn(yy, xx))
                visited[yy][xx] = true;
        }
        
        for(int yy = y; yy >= y - d; yy--) {
            int xx = yy - diff + d;
            if(isIn(yy, xx))
                visited[yy][xx] = true;
            
            xx = -yy + sum - d;
            if(isIn(yy, xx))
                visited[yy][xx] = true;
        }
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```