# 25_02_27_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 미로 탈출

[](https://school.programmers.co.kr/learn/courses/30/lessons/159993)

```java
import java.util.Queue;
import java.util.LinkedList;

class Solution {
    private int R = 0;
    private int C = 0;
    private char[][] map;
    
    private int sy = 0;
    private int sx = 0;
    
    public int solution(String[] maps) {
        R = maps.length;
        C = maps[0].length();
        
        map = new char[R][C];
        
        for(int r = 0; r < R; r++)
            for(int c = 0; c < C; c++) {
                map[r][c] = maps[r].charAt(c);
                
                if(map[r][c] == 'S') {
                    sy = r;
                    sx = c;
                }
            }
        
        return bfs();
    }
    
    private int bfs() {
        Queue<int[]> queue = new LinkedList<>();
        boolean[][][] visited = new boolean[R][C][2];
        
        visited[sy][sx][0] = true;
        
        //  현재 위치 (y, x), 레버 상태 (0 : 레버 당기기 전, 1 : 레버 당겨짐), 시간
        queue.offer(new int[] {sy, sx, 0, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];
            int cstatus = cur[2];
            int ctime = cur[3];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                int nstatus = cstatus;
                
                //  (ny, nx)가 범위 내에 있고, 벽이 아니며, 현재 레버 상태로 방문한 적이 없을 경우
                if(isIn(ny, nx) && map[ny][nx] != 'X' && !visited[ny][nx][cstatus]) {
                    if(cstatus == 1 && map[ny][nx] == 'E')  //  레버 당겨진 상태에서 출구 도착한 경우
                        return ctime + 1;
                    
                    visited[ny][nx][cstatus] = true;
                    
                    if(cstatus == 0 && map[ny][nx] == 'L')  //  레버를 발견한 경우, 레버 당김
                        nstatus = 1;
                    
                    queue.offer(new int[] {ny, nx, nstatus, ctime + 1});
                }
            }
        }
        
        
        return -1;  //  도착하지 못함
    }
    
    private final int[] dy = {0, 1, 0, -1};
    private final int[] dx = {1, 0, -1, 0};
    
    private boolean isIn(int y, int x) {
        return (0 <= y && y < R) && (0 <= x && x < C);
    }
}
```

### 프로그래머스 가장 긴 팰린드롬

[](https://school.programmers.co.kr/learn/courses/30/lessons/12904)

```java
class Solution {
    public int solution(String s) {
        //  dp[from][to] : s의 부분문자열 s[from:to]에 존재하는 가장 긴 팰린드롬의 길이
        int[][] dp = new int[s.length()][s.length()];

        for(int k = 0; k < s.length(); k++) {  
            for(int y = 0; y < s.length() - k; y++) {
                int x = y + k;
                
                if(y == x)  //  길이 1인 부분문자열
                    dp[y][x] = 1;
                else if(x == y + 1) //  길이 2인 부분문자열
                    dp[y][x] = s.charAt(y) == s.charAt(x) ? 2 : 1;
                else {  //  길이 3 이상인 부분문자열
                    int head = s.charAt(y);
                    int tail = s.charAt(x);
                    
                    //  y번째 문자와 x번째 문자가 같고 s[y + 1:x - 1] 전체가 팰린드롬일 경우
                    if(head == tail && dp[y + 1][x - 1] == x - y - 1)
                        dp[y][x] = x - y + 1;   //  s[y:x] 전체가 팰린드롬
                    else
                        dp[y][x] = Math.max(dp[y + 1][x], dp[y][x - 1]);
                }
            }
        }
        
        return dp[0][s.length() - 1];
    }
}
```