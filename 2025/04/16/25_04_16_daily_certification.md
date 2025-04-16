# 25_04_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24513 좀비 바이러스

[24513번: 좀비 바이러스](http://boj.ma/24513/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final int NONE = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[][] map;
    private static int[][][] visited;    //    visited[nth][y][x] : (nth + 1)번째 바이러스가 (y, x)에 방문한 시각
   
    private static final Queue<int[]> queue = new LinkedList<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new int[N][M];
        visited = new int[2][N][M];
        
        for(int nth = 0; nth < 2; nth++) {
            for(int y = 0; y < N; y++)
                Arrays.fill(visited[nth][y], NONE);
        }
        
        for(int y = 0; y < N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 0; x < M; x++) {
                map[y][x] = Integer.parseInt(st.nextToken());
                if(map[y][x] == 1 || map[y][x] == 2) {
                    queue.offer(new int[] {y, x, map[y][x]});
                    visited[map[y][x] - 1][y][x] = 0;
                }
            }
        }
        
        bfs();
    }    //    main-end
    
    private static void bfs() {
        int time = 1;
    
        while(!queue.isEmpty()) {
            int size = queue.size();
            
            while(size-- > 0) {
                int[] cur = queue.poll();
                int cy = cur[0];
                int cx = cur[1];
                int cv = cur[2];
                int ov = cv == 1 ? 2 : 1;

                if(visited[cv - 1][cy][cx] == visited[ov - 1][cy][cx])    //    바이러스 1, 2에 동시에 감염된 경우
                	continue;
            
                for(int d = 0; d < 4; d++) {
                    int ny = cy + dy[d];
                    int nx = cx + dx[d];
                
                    if(isIn(ny, nx) && map[ny][nx] != -1) {             //    범위 안이고 백신이 없을 경우
                        if(visited[cv - 1][ny][nx] == NONE) {           //    아직 cv 바이러스가 침투하지 않은 경우
                           if(visited[ov - 1][ny][nx] == NONE || visited[ov - 1][ny][nx] == time) {        //    ov 바이러스가 없거나 이번 시간에 퍼진 경우
                               visited[cv - 1][ny][nx] = time;
                               queue.offer(new int[] {ny, nx, cv});
                            }
                        }
                    }
                }
            }
            
            time++;
        }
    
        int[] cnts = new int[3];
        
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < M; x++) {
                if(visited[0][y][x] == visited[1][y][x] && visited[0][y][x] != NONE)
                    cnts[2]++;
                else if(visited[0][y][x] != NONE && visited[1][y][x] == NONE)
                    cnts[0]++;
                else if(visited[0][y][x] == NONE && visited[1][y][x] != NONE)
                    cnts[1]++;
            }
        }
    
        for(int v = 0; v < 3; v++)
            System.out.print(cnts[v] + " ");
        System.out.println();
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```