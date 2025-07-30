# 25_07_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11967 불켜기

[11967번: 불켜기](http://boj.ma/11967/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    map 크기 N * N
    private static int M;    //    스위치 개수
    
    //    map[y][x] : (y, x)에 불이 켜져있으면 true, 꺼져있으면 false
    private static boolean[][] map;
    //    visited[y][x] : (y, x)로 이동한 적이 있으면 true, 없으면 false
    private static boolean[][] visited;
    
    //    switchMap[y][x] : (y, x) 방에서 불을 켤 수 있는 방의 좌표 큐
    private static List<int[]>[][] switchMap;
    
    private static int answer = 1;    //    최대로 불 켜진 방의 개수, (0, 0)은 켜짐
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new boolean[N][N];
        visited = new boolean[N][N];
        switchMap = new ArrayList[N][N];
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < N; x++)
                switchMap[y][x] = new ArrayList<>();
        }
        
        for(int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            int y1 = Integer.parseInt(st.nextToken()) - 1;
            int x1 = Integer.parseInt(st.nextToken()) - 1;
            int y2 = Integer.parseInt(st.nextToken()) - 1;
            int x2 = Integer.parseInt(st.nextToken()) - 1;
            
            switchMap[y1][x1].add(new int[] {y2, x2});    //    (y1, x1)에서 (y2, x2) 방의 불을 킬 수 있음
        }
        
        bfs();
    }    //    main-end
    
    private static void bfs() {
        Queue<int[]> queue = new LinkedList<>();
        map[0][0] = true;        //    (0, 0)은 처음부터 불이 켜져 있음
        visited[0][0] = true;
        
        queue.offer(new int[] {0, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];
            
            for(int[] pos : switchMap[cy][cx]) {    //    (cy, cx)에 있는 스위치 누르기
                int py = pos[0];
                int px = pos[1];
                
                if(!map[py][px]) {        //    불이 안켜졌을 경우
                    map[py][px] = true;
                    answer++;
                    
                    for(int d = 0; d < 4; d++) {
                        int ny = py + dy[d];
                        int nx = px + dx[d];    //    (py, px)와 인접한 칸
                        
                        //    (ny, nx)를 이미 방문했는데, 그 당시에는 (py, px)에 불이 켜지지 않아서 (ny, nx)에서 (py, px)로 이동하지 못했을 경우
                        if(isIn(ny, nx) && visited[ny][nx] && !visited[py][px]) {
                            visited[py][px] = true;
                            queue.offer(new int[] {py, px});    //    (ny, nx)에서 (py, px)로 이동한것처럼 처리
                        }
                    }
                }
            }
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    (cy, cx)와 인접한 칸
                
                if(isIn(ny, nx) && !visited[ny][nx] && map[ny][nx]) {
                    visited[ny][nx] = true;
                    queue.offer(new int[] {ny, nx});
                }
            }
        }
        
        System.out.println(answer);
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < N);
    }
}    //    Main-class-end
```