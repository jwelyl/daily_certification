# 26_03_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2151 거울 설치

[2151번: 거울 설치](http://boj.ma/2151/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Queue;

public class Main {
  private static final int INF = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    int sy = -1;
    int sx = -1;
    int ey = -1;
    int ex = -1;
    int answer = INF;

    char[][] map = new char[n][n];
    int[][][] visited = new int[4][n][n];

    for(int d = 0; d < 4; d++) {
      for(int i = 0; i < n; i++)
        Arrays.fill(visited[d][i], INF);
    }

    for(int y = 0; y < n; y++) {
      map[y] = br.readLine().toCharArray();
      for(int x = 0; x < n; x++) {
        if(map[y][x] == '#') {
          if(sy == -1 && sx == -1) {
            sy = y;
            sx = x;
          } else {
            ey = y;
            ex = x;
          }
        }
      }
    }

    bfs(n, sy, sx, map, visited);
    for(int d = 0; d < 4; d++)
      answer = Math.min(answer, visited[d][ey][ex]);

    System.out.println(answer);
  }	//	main

  private static final int[] dy = {-1, 0, 1,  0};
  private static final int[] dx = { 0, 1, 0, -1};

  private static boolean isIn(int y, int x, int n) {
    return (0 <= y && y < n) && (0 <= x && x < n);
  }

  private static void bfs(int n, int sy, int sx, char[][] map, int[][][] visited) {
    Queue<int[]> queue = new ArrayDeque<>();
    for(int d = 0; d < 4; d++)
      visited[d][sy][sx] = 0;
    queue.offer(new int[] {sy, sx, -1, 0});

    while(!queue.isEmpty()) {
      int[] out = queue.poll();
      int cy = out[0];
      int cx = out[1];
      int cdir = out[2];
      int cturn = out[3];
      char mark = map[cy][cx];
      int ndir;

      if(cdir == -1 || mark == '!') {
        for(int d = 0; d < 4; d++) {
          int ny = cy + dy[d];
          int nx = cx + dx[d];
          int nturn = cturn;
          ndir = d;

          if(!isIn(ny, nx, n) || map[ny][nx] == '*')
            continue;

          nturn += ((cdir == -1) ? 0 : ((cdir != ndir) ? 1 : 0));

          if(visited[ndir][ny][nx] > nturn) {
            visited[ndir][ny][nx] = nturn;
            queue.offer(new int[] {ny, nx, ndir, nturn});
          }
        }
      }
      else {
        ndir = cdir;
        int ny = cy + dy[ndir];
        int nx = cx + dx[ndir];

        if(!isIn(ny, nx, n) || map[ny][nx] == '*')
          continue;

        if(visited[ndir][ny][nx] > cturn) {
          visited[ndir][ny][nx] = cturn;
          queue.offer(new int[] {ny, nx, ndir, cturn});
        }
      }
    }
  }
} //  Main-class-end
```