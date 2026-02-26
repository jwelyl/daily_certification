# 26_02_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12698 Portal (Large)

[http://boj.ma/12698/t](http://boj.ma/12698/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.HashSet;
import java.util.Queue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int n = Integer.parseInt(br.readLine());  //	테스트케이스 개수

    for(int tc = 1; tc <= n; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int r = Integer.parseInt(st.nextToken());
      int c = Integer.parseInt(st.nextToken());
      int sy = -1;
      int sx = -1;
      int ey = -1;
      int ex = -1;

      char[][] map = new char[r][c];
      Set<Integer>[] graph = new HashSet[r * c];
      for(int y = 0; y < r; y++) {
        map[y] = br.readLine().toCharArray();
        for(int x = 0; x < c; x++) {
          graph[y * c + x] = new HashSet<>();
          if(map[y][x] == 'O') {
            sy = y;
            sx = x;
          }
          else if(map[y][x] == 'X') {
            ey = y;
            ex = x;
          }
        }
      }

      makeGraph(r, c, map, graph);

      sb.append("Case #").append(tc).append(": ");
      sb.append(bfs(r, c, sy, sx, ey, ex, graph)).append("\n");
    }

    System.out.print(sb);
  }    //    main-end

  private static void makeGraph(int r, int c, char[][] map, Set<Integer>[] graph) {
    for (int cy = 0; cy < r; cy++) {
      for (int cx = 0; cx < c; cx++) {
        int wallCnt = 0;
        int onlyDir = -1;

        if (map[cy][cx] == '#') continue;          // 벽은 정점 아님
        int cnum = cy * c + cx;

        // 1) 인접한 빈 칸 간선 (양방향)
        for (int d = 0; d < 4; d++) {
          int ny = cy + dy[d], nx = cx + dx[d];
          if (isIn(ny, nx, r, c) && map[ny][nx] != '#') {
            int nnum = ny * c + nx;
            graph[cnum].add(nnum);
            graph[nnum].add(cnum);
          }
        }

        // 2) 포털 간선
        // 현재 칸의 인접 4면 중 벽/바깥이 하나도 없으면 포털 사용 불가
        for (int d = 0; d < 4; d++) {
          int ny = cy + dy[d];
          int nx = cx + dx[d];
          if (!isIn(ny, nx, r, c) || map[ny][nx] == '#') {
            wallCnt++;
            onlyDir = d; // 마지막으로 본 벽 방향(벽이 1개일 때 그 방향)
          }
        }
        if (wallCnt == 0)
          continue;

        for (int d2 = 0; d2 < 4; d2++) {
          if (wallCnt == 1 && d2 == onlyDir)
            continue;

          int ny = cy;
          int nx = cx;

          while (true) {
            int ty = ny + dy[d2];
            int tx = nx + dx[d2];
            if (!isIn(ty, tx, r, c) || map[ty][tx] == '#')
              break;
            ny = ty;
            nx = tx;
          }
          int nnum = ny * c + nx;
          if (nnum != cnum)
            graph[cnum].add(nnum);
        }
      }
    }
  }

  private static String bfs(int r, int c, int sy, int sx, int ey, int ex, Set<Integer>[] graph) {
    Queue<int[]> queue = new ArrayDeque<>();
    boolean[] visited = new boolean[r * c];

    visited[sy * c + sx] = true;
    queue.offer(new int[] {sy * c + sx, 0});

    while(!queue.isEmpty()) {
      int[] cur = queue.poll();
      int cv = cur[0];
      int cc = cur[1];

      for(int nv : graph[cv]) {
        if(nv == ey * c + ex)
          return (cc + 1) + "";

        if(!visited[nv]) {
          visited[nv] = true;
          queue.offer(new int[] {nv, cc + 1});
        }
      }
    }

    return "THE CAKE IS A LIE";
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static boolean isIn(int y, int x, int r, int c) {
    return (0 <= y && y < r) && (0 <= x && x < c);
  }
}    //    Main-class-end
```