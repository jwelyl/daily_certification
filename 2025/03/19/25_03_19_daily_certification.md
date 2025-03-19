# 25_03_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4179 불!

[4179번: 불!](http://boj.ma/4179/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static char[][] map;
    
    private static final Queue<int[]> queueJ = new LinkedList<>();
    private static final Queue<int[]> queueF = new LinkedList<>();
    
    private static boolean[][] visited;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new char[N + 2][M + 2];
        visited = new boolean[N + 2][M + 2];
        for(int y = 1; y <= N; y++) {
            String input = br.readLine();
            
            for(int x = 1; x <= M; x++) {
                char ch = input.charAt(x - 1);
                map[y][x] = ch;
                
                if(ch == 'J') {
                    visited[y][x] = true;
                    queueJ.add(new int[] {y, x, 0});
                }
                else if(ch == 'F') {
                    visited[y][x] = true;
                    queueF.add(new int[] {y, x});
                }
            }
        }
        
        bfs();
    }    //    main-end
    
    private static void bfs() {
        while(!queueJ.isEmpty()) {
            int jSize = queueJ.size();
            int fSize = queueF.size();
            
            //   불이 먼저 이동
            for(int i = 0; i < fSize; i++) {
                int[] cur = queueF.poll();
                int cy = cur[0];
                int cx = cur[1];            //    현재 불 위치
                
                for(int d = 0; d < 4; d++) {
                    int ny = cy + dy[d];
                    int nx = cx + dx[d];
                    
                    if(isIn(ny, nx) && map[ny][nx] != '#' && map[ny][nx] != 'F') {
                    	if(isEnd(ny, nx))
                    		continue;
                    	
                        queueF.offer(new int[] {ny, nx});
                        map[ny][nx] = 'F';
                    }
                }
            }
            
            //    그 다음 지훈이가 이동
            for(int i = 0; i < jSize; i++) {
                int[] cur = queueJ.poll();
                int cy = cur[0];
                int cx = cur[1];            //    현재 지훈이 위치
                int ctime = cur[2];         //    지훈이가 (cy, cx)까지 오는데 걸린 시간
                
                for(int d = 0; d < 4; d++) {
                    int ny = cy + dy[d];
                    int nx = cx + dx[d];
                    
                    //    다음 칸이 범위 안이고, 벽이나 불이 아니며, 지훈이가 온 적도 없을때
                    if(isIn(ny, nx) && map[ny][nx] != '#' && map[ny][nx] != 'F' && !visited[ny][nx]) {
                        if(isEnd(ny, nx)) {   //    무사히 탈출한 경우
                            System.out.println(ctime + 1);
                            return;
                        }
                        
                        map[ny][nx] = 'J';
                        visited[ny][nx] = true;
                        queueJ.offer(new int[] {ny, nx, ctime + 1});
                    }
                }
            }
        }
            
        System.out.println("IMPOSSIBLE");
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y <= N + 1) && (0 <= x && x <= M + 1);
    }
    
    private static boolean isEnd(int y, int x) {
        return y == 0 || y == N + 1 || x == 0 || x == M + 1;
    }
}    //    Main-class-end
```