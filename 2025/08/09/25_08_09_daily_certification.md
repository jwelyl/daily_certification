# 25_08_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1261 알고스팟

[1261번: 알고스팟](http://boj.ma/1261/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.LinkedList;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static char[][] map;
    private static int[][] dist;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        M = Integer.parseInt(st.nextToken());
        N = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        dist = new int[N][M];
        
        for(int y = 0; y < N; y++) {
            map[y] = br.readLine().toCharArray();
            Arrays.fill(dist[y], N * M);
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Deque<int[]> deque = new LinkedList<>();
        dist[0][0] = 0;
        deque.offerFirst(new int[] {0, 0, dist[0][0]});
        
        while(!deque.isEmpty()) {
            int[] cur = deque.pollFirst();
            int cy = cur[0];
            int cx = cur[1];
            int cc = cur[2];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                int nc = cc;
                
                if(isIn(ny, nx)) {
                    nc += map[ny][nx] == '0' ? 0 : 1;
                    
                    if(nc < dist[ny][nx]) {
                        dist[ny][nx] = nc;
                        if(map[ny][nx] == '0')
                            deque.offerFirst(new int[] {ny, nx, dist[ny][nx]});
                        else
                            deque.offerLast(new int[] {ny, nx, dist[ny][nx]});
                    }
                }
            }
        }
        
        return dist[N - 1][M - 1];
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end

```

### BOJ 9376 탈옥

[9376번: 탈옥](http://boj.ma/9376/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.LinkedList;
import java.util.Arrays;

public class Main {
    private static final char WALL = '*';
    private static final char EMPTY = '.';
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;
    private static int H;
    private static int W;
    
    private static char[][] map;    //    감옥 평면도
    
    private static int[][] dist0;   //    (0, 0)에서 출발했을때 각 지점까지의 최단거리

    private static int sy1 = -1;
    private static int sx1 = -1;    //    죄수 1의 시작 위치
    private static int[][] dist1;   //    (sy1, sx1)에서 출발했을때 각 지점까지의 최단거리
    
    private static int sy2 = -1;
    private static int sx2 = -1;    //    죄수 2의 시작 위치
    private static int[][] dist2;   //    (sy2, sx2)에서 출발했을때 각 지점까지의 최단거리
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            st = new StringTokenizer(br.readLine());
            H = Integer.parseInt(st.nextToken());
            W = Integer.parseInt(st.nextToken());
            
            map = new char[H + 2][W + 2];
            dist0 = new int[H + 2][W + 2];
            dist1 = new int[H + 2][W + 2];
            dist2 = new int[H + 2][W + 2];
            
            sy1 = -1;
            sx1 = -1;
            sy2 = -1;
            sx2 = -1;
            
            answer = H * W;
            
            for(int y = 1; y <= H; y++) {
                String input = br.readLine();
                for(int x = 1; x <= W; x++) {
                    map[y][x] = input.charAt(x - 1);
                    
                    if(map[y][x] == '$') {
                        map[y][x] = EMPTY;
                        
                        if(sy1 == -1 && sx1 == -1) {
                            sy1 = y;
                            sx1 = x;
                        }
                        else {
                            sy2 = y;
                            sx2 = x;
                        }
                    }
                }
            }
            
            bfs(0, 0, dist0);
            bfs(sy1, sx1, dist1);
            bfs(sy2, sx2, dist2);
            
            //    (0, 0)에서 (sy1, sx1)까지의 최단거리 + (sy2, sx2)까지의 최단거리
            answer = dist0[sy1][sx1] + dist0[sy2][sx2];
            
            for(int y = 1; y <= H; y++) {
                for(int x = 1; x <= W; x++)
                    //    (y, x)에서 (0, 0)까지의 최단거리 + (sy1, sx1)까지의 최단거리 + (sy2, sx2)까지의 최단거리
                    //    (y, x)가 문일 경우 (0, 0) 출발, (sy1, sx1) 출발, (sy2, sx2) 출발 세 경우 모두 +1 됨, -2로 보정
                    answer = Math.min(answer, dist0[y][x] + dist1[y][x] + dist2[y][x] + (map[y][x] == '#' ? -2 : 0));
            }
            
            sb.append(answer).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void bfs(int sy, int sx, int[][] dist) {
        Deque<int[]> deque = new LinkedList<>();
        for(int y = 0; y < H + 2; y++)
            Arrays.fill(dist[y], H * W + 1);
        
        dist[sy][sx] = 0;
        deque.offerFirst(new int[] {sy, sx, dist[sy][sx]});
        
        while(!deque.isEmpty()) {
            int[] cur = deque.pollFirst();
            int cy = cur[0];
            int cx = cur[1];
            int cc = cur[2];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                int nc = cc;
                
                if(isIn(ny, nx) && map[ny][nx] != WALL) {
                    nc += map[ny][nx] == '#' ? 1 : 0;
                    
                    if(nc < dist[ny][nx]) {
                        dist[ny][nx] = nc;
                        
                        if(nc == cc)
                            deque.offerFirst(new int[] {ny, nx, nc});
                        else
                            deque.offerLast(new int[] {ny, nx, nc});
                    }
                }
            }
        }
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < H + 2) && (0 <= x && x < W + 2);
    }
}    //    Main-class-end
```