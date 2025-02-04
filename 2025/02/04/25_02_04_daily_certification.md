# 25_02_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15573 채굴

[15573번: 채굴](http://boj.ma/15573/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final int EMPTY = 0;    //    지나갈 수는 있지만 광물로 카운트되지 않는 칸
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;    //    채취해야 할 최소 광물의 수
    
    private static int[][] map;
    
    private static int maxD = 0;    //   존재하는 광물 강도 중 가장 높은 강도
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new int[N + 1][M + 2];
        for(int y = 1; y <= N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 1; x <= M; x++) {
                int d = Integer.parseInt(st.nextToken());    //    (y, x)칸의 광물 강도
                
                map[y][x] = d;
                maxD = Math.max(maxD, d);
            }
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 1;
        int end = maxD;
        int res = maxD;    //    res 이하의 강도를 가지는 광물들만 채굴 가능할 때 K개 이상 채굴할 수 있는 최소 강도
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    mid 이하의 광물만 채굴한다고 가정
            
            if(bfs(mid)) {            //    mid 이하의 광물만 채굴할때 K개 이상 채굴 가능할 경우
                res = mid;            //    일단 mid 저장
                end = mid - 1;        //    더 작은 강도의 광물만 채굴해도 가능한지 체크
            }
            else
                start = mid + 1;        //    광물 강도 한도 늘려야 함
        }
        
        return res;
    }
    
    //    limit 이하의 광물만 채굴했을때 K개 이상 채굴할 수 있으면 true
    private static boolean bfs(int limit) {
        Queue<int[]> queue = new LinkedList<>();
        boolean[][] visited = new boolean[N + 1][M + 2];
        int cnt = 0;    //    채굴한 광물 수
        
        visited[0][0] = true;
        queue.offer(new int[] {0, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];    //    현재 위치
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    확인할 위치
                
                //    확인한 위치가 범위 안이고 limit 이하이며, 아직 방문하지 않은 경우
                if(isIn(ny, nx) && map[ny][nx] <= limit && !visited[ny][nx]) {
                    visited[ny][nx] = true;
                    
                    if(map[ny][nx] != EMPTY) {    //    실제 limit 이하의 광물이 있을 경우
                        cnt++;    //    채굴한 광물의 개수 1 증가                   
                        if(cnt == K)    //    K개의 광물을 채굴한 경우
                            return true;
                    }
                    
                    queue.offer(new int[] {ny, nx});
                }
            }
        }
        
        return false;    //    K개 이상 채굴할 수 없음
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y <= N) && (0 <= x && x <= M + 1);
    }
}    //    Main-class-end
```