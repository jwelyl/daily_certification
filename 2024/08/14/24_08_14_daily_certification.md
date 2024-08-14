# 24_08_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15730 수영장 사장님

[](https://www.acmicpc.net/problem/15730)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Deque;
import java.util.LinkedList;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[][] heights;         // 수영장 바닥 높이
    private static int[][] poolHeights;     // 물을 포함한 높이
    private static final Deque<int[]>[] deques = new Deque[10_001];
    
    private static int hMax;    // 수영장 바닥 높이 중 가장 높은 높이
    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        heights = new int[N + 2][M + 2];
        poolHeights = new int[N + 2][M + 2];
        
        for(int i = 0; i <= 10_000; i++)
            deques[i] = new LinkedList<>();
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 1; j <= M; j++) {
                heights[i][j] = Integer.parseInt(st.nextToken());
                hMax = Math.max(hMax, heights[i][j]);
            }
        }
        
        // poolHeights 초기화
        for(int i = 1; i <= N; i++) {
            for(int j = 1; j <= M; j++)
                poolHeights[i][j] = heights[i][j];
        }

        bfs();
        
        for(int i = 1; i <= N; i++) {
            for(int j = 1; j <= M; j++)
                ans += poolHeights[i][j] - heights[i][j];
        }
        
        System.out.println(ans);
    }   // main-end
    
    private static void bfs() {
        boolean[][] visited = new boolean[N + 2][M + 2];
        deques[0].offer(new int[] {0, 0});
        
        for(int h = 0; h <= 10_000; h++) {
            while(!deques[h].isEmpty()) {
                int[] out = deques[h].poll();
                int cy = out[0];
                int cx = out[1];
                
                if(visited[cy][cx])
                    continue;
                
                visited[cy][cx] = true;
                poolHeights[cy][cx] = h;
                
                for(int d = 0; d < 4; d++) {
                    int ny = cy + dy[d];
                    int nx = cx + dx[d];
                    
                    if(isIn(ny, nx)) {
                        if(visited[ny][nx])
                            continue;
                        
                        if(heights[ny][nx] <= h)
                            deques[h].offer(new int[] {ny, nx});
                        else
                            deques[heights[ny][nx]].offer(new int[] {ny, nx});
                    }
                }
            }
        }
    }   // bfs-end
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y <= N + 1) && (0 <= x && x <= M + 1);
    }
}   // Main-class-end
```