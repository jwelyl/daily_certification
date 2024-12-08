# 24_12_08_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 16973** 직사각형 탈출

[16973번: 직사각형 탈출](https://boj.ma/16973/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[][] prefixSum;

    private static int H;
    private static int W;
    private static int SR;
    private static int SC;
    private static int FR;
    private static int FC;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        prefixSum = new int[N + 1][M + 1];
        
        for(int r = 1; r <= N; r++) {
            st = new StringTokenizer(br.readLine());
            for(int c = 1; c <= M; c++)
                prefixSum[r][c] = prefixSum[r][c - 1] + prefixSum[r - 1][c] - prefixSum[r - 1][c - 1] + Integer.parseInt(st.nextToken());
        }
        
        st = new StringTokenizer(br.readLine());
        H = Integer.parseInt(st.nextToken());
        W = Integer.parseInt(st.nextToken());
        SR = Integer.parseInt(st.nextToken());
        SC = Integer.parseInt(st.nextToken());
        FR = Integer.parseInt(st.nextToken());
        FC = Integer.parseInt(st.nextToken());
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        if(SR == FR && SC == FC)
            return 0;
        
        Queue<int[]> queue = new LinkedList<>();
        boolean[][] visited = new boolean[N + 1][M + 1];
        visited[SR][SC] = true;
        queue.offer(new int[] {SR, SC, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cr = cur[0];
            int cc = cur[1];
            int ccost = cur[2];
            
            for(int d = 0; d < 4; d++) {
                int nr = cr + dy[d];
                int nc = cc + dx[d];
                int ncost = ccost + 1;
                
                if(isIn(nr, nc) && isIn(nr + H - 1, nc + W - 1) &&
                     partialSum(nr, nc, nr + H - 1, nc + W - 1) == 0 && !visited[nr][nc]) {
                    if(nr == FR && nc == FC)    //    도착한 경우
                        return ncost;
                    
                    visited[nr][nc] = true;
                    queue.offer(new int[] {nr, nc, ncost});
                }
            }
        }
        
        return -1;    //    도달할 수 없을 경우
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int r, int c) {
        return (1 <= r && r <= N) && (1 <= c && c <= M);
    }
    
    //    좌상단이 (r1, c1), 우하단이 (r2, c2)인 부분 직사각형의 부분합
    private static int partialSum(int r1, int c1, int r2, int c2) {
        return prefixSum[r2][c2] - prefixSum[r1 - 1][c2] - prefixSum[r2][c1 - 1] + prefixSum[r1- 1][c1 - 1];
    }
}    //    Main-class-end
```