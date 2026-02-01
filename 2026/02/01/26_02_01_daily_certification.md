# 26_02_01_daily_certification

```
[#032 koreii] 데일리인증 20260201
1 알고리즘 문제 풀이
- 0-1 BFS, Simulation (BOJ 5827 What's Up With Gravity) 
```

# Problem Solving (Algorithm & SQL)

### BOJ 5827 What's Up With Gravity

[5827번: What's Up With Gravity](http://boj.ma/5827/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
  private static final int NONE = -1;
  private static final int INF = Integer.MAX_VALUE;

  private static final char C = 'C';  //  출발지
  private static final char D = 'D';  //  목적지
  private static final char E = '.';  //  빈칸
  private static final char W = '#';  //  벽

  private static final int F = 0;     //  정방향 (아랫방향)
  private static final int R = 1;     //  역방향 (윗방향)

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken()); //  격자 크기

    char[][] map = new char[n][m];
    int sy = 0;
    int sx = 0;
    int ey = 0;
    int ex = 0;

    for(int y = 0; y < n; y++) {
      map[y] = br.readLine().toCharArray();
      for(int x = 0; x < m; x++) {
        if(map[y][x] == C) {
          sy = y;
          sx = x;
        }
        else if(map[y][x] == D) {
          ey = y;
          ex = x;
        }
      }
    }

    //  처음 위치에서 중력 방향으로 떨어졌을때 y 좌표
    sy = fall(sy, sx, 1, n, m, map);

    if (sy == NONE)
      System.out.println(NONE);
    else
      System.out.println(bfs(sy, sx, ey, ex, n, m, map));
  } // main-end

  private static int bfs(int sy, int sx, int ey, int ex, int n, int m, char[][] map) {
    Deque<int[]> deque = new ArrayDeque<>();
    int[][][] dist = new int[2][n][m];
    int res;

    for(int i = 0; i < 2; i++) {
      for(int j = 0; j < n; j++)
        Arrays.fill(dist[i][j], INF);
    }

    dist[F][sy][sx] = 0;
    deque.offer(new int[] {sy, sx, F, dist[F][sy][sx]});

    while(!deque.isEmpty()) {
      int[] cur = deque.pollFirst();
      int cy = cur[0];
      int cx = cur[1];
      int cd = cur[2];
      int ccost = cur[3];
      int cdir = cd == F ? 1 : -1;

      //  1. 좌우로 이동
      for(int d = 0; d < 2; d++) {
        int ny = cy;
        int nx = cx + dx[d];

        if(isIn(ny, nx, n, m) && map[ny][nx] != W) {
          //  1-1. 좌우 바로 옆에 D가 있을 경우
          if(map[ny][nx] == D) {
            if (dist[cd][ny][nx] > ccost)
              dist[cd][ny][nx] = ccost;
          }
          //  좌우로 이동 후 중력 방향으로 떨어뜨리기
          ny = fall(ny, nx, cdir, n, m, map);
          if(ny != NONE) {
            if(dist[cd][ny][nx] > ccost) {
              dist[cd][ny][nx] = ccost;
              deque.offerFirst(new int[] {ny, nx, cd, ccost});
            }
          }
        }
      }
      //  2. 중력 방향 바꾸어 떨어뜨리기
      cy = fall(cy, cx, -1 * cdir, n, m, map);

      if(cy != NONE) {
        if(dist[1 - cd][cy][cx] > ccost + 1) {
          dist[1 - cd][cy][cx] = ccost + 1;
          deque.offerLast(new int[] {cy, cx, 1 - cd, ccost + 1});
        }
      }
    }

    res = Math.min(dist[F][ey][ex], dist[R][ey][ex]);

    return res == INF ? -1 : res;
  }

  private static final int[] dx = {-1, 1};

  private static boolean isIn(int y, int x, int n, int m) {
    return 0 <= y && y < n && 0 <= x && x < m;
  }

  //  현재 칸 (cy, cx)에서 중력 방향으로 떨어질 수 있을때까지 떨어져서 도달한 칸의 y 좌표
  private static int fall(int cy, int cx, int dir, int n, int m, char[][] map) {
    while(isIn(cy + dir, cx, n, m) && map[cy + dir][cx] == E) //  중력 방향 아래칸이 존재하고 빈칸일 경우
      cy += dir;

    if(!isIn(cy + dir, cx, n, m)) //  아래칸이 존재하지 않을 경우
      return NONE;

    if(map[cy + dir][cx] == '#')  //  아래칸이 벽일 경우
      return cy;
    else  //  아래칸이 D일 경우, 아래칸까지 가기
      return cy + dir;
  }
} // Main-class-end
```