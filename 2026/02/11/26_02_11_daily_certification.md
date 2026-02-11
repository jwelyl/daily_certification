# 26_02_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 35184 수돗물과 게 임

[35184번: 수돗물과 게 임](http://boj.ma/35184/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static final char S = 'S';
  private static final char T = 'T';

  private static final int INF = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    int t = Integer.parseInt(st.nextToken()); //  게가 몸을 반시계방향으로 90도 돌리는 시간

    char[][] map = new char[n][m];
    int sy = -1;
    int sx = -1;

    for(int y = 0; y < n; y++) {
      map[y] = br.readLine().toCharArray();
      for(int x = 0; x < m; x++) {
        if('0' <= map[y][x] && map[y][x] <= '3') {
          sy = y;
          sx = x;
        }
      }
    }

    System.out.println(dijkstra(n, m, t, map, sy, sx));
  }    //    main-end

  private static int dijkstra(int n, int m, int t, char[][] map, int sy, int sx) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    //  dist[dir][y][x] : (y, x)에 몸 방향이 dir인 상태로 도달하는 최소 시간
    int[][][] dist = new int[4][n][m];

    for(int dir = 0; dir < 4; dir++) {
      for (int y = 0; y < n; y++)
        Arrays.fill(dist[dir][y], INF);
    }

    dist[map[sy][sx] - '0'][sy][sx] = 0;
    pq.offer(new Node(sy, sx, map[sy][sx] - '0', dist[map[sy][sx] - '0'][sy][sx]));

    while(!pq.isEmpty()) {
      Node cur = pq.poll();
      int cy = cur.y;
      int cx = cur.x;
      int cdir = cur.dir;
      int ccost = cur.cost;

      if(ccost > dist[cdir][cy][cx])
        continue;

      if(map[cy][cx] == S)
        return ccost;

      if(map[cy][cx] == T) {  //  현재 칸이 수돗물 칸일 경우
        int ndir = (cdir + 1) % 4;  //  바라보는 방향 변경
        int ny = cy + dy[ndir];
        int nx = cx + dx[ndir];     //  바라보는 방향으로 추가 시간 없이 한칸 이동

        if(isIn(ny, nx, n, m) && ccost < dist[ndir][ny][nx]) {
          dist[ndir][ny][nx] = ccost;
          pq.offer(new Node(ny, nx, ndir, dist[ndir][ny][nx]));
        }
      }
      else {  //  현재 칸이 수돗물이 아닐 경우
        //  1. 몸을 돌리지 않고 이동 시도
        for(int d = 0; d < 2; d++) {
          int ny = cy + dyy[cdir][d];
          int nx = cx + dxx[cdir][d];
          int ncost = ccost + 1;

          if(isIn(ny, nx, n, m) && ncost < dist[cdir][ny][nx]) {
            dist[cdir][ny][nx] = ncost;
            pq.offer(new Node(ny, nx, cdir, dist[cdir][ny][nx]));
          }
        }
        //  2. 몸을 돌리기
        int ndir = (cdir + 1) % 4;    //  몸을 돌리기만 했으므로 위치는 그대로
        int ncost = ccost + t;

        if(ncost < dist[ndir][cy][cx]) {
          dist[ndir][cy][cx] = ncost;
          pq.offer(new Node(cy, cx, ndir, dist[ndir][cy][cx]));
        }
      }
    }

    return -1;
  }

  private static final int[] dy = {-1, 0, 1, 0};
  private static final int[] dx = {0, -1, 0, 1};

  //  dy[dir] : 게가 바라보는 방향이 dir일때 y축 방향으로 이동할 수 있는 방향
  //  dx[dir] : 게가 바라보는 방향이 dir일때 x축 방향으로 이동할 수 있는 방향
  private static final int[][] dyy = {{0, 0}, {-1, 1}, {0, 0}, {-1, 1}};
  private static final int[][] dxx = {{-1, 1}, {0, 0}, {-1, 1}, {0, 0}};

  private static boolean isIn(int y, int x, int n, int m) {
//    System.out.println("y = " + y + ", x = " + x);
//    System.out.println("n = " + n + ", m = " + m);
    return (0 <= y && y < n) && (0 <= x && x < m);
  }

  private static class Node implements Comparable<Node> {
    public int y;
    public int x;     //  현재 위치
    public int dir;   //  현재 바라보는 방향
    public int cost;

    public Node(int y, int x, int dir, int cost) {
      this.y = y;
      this.x = x;
      this.dir = dir;
      this.cost = cost;
    }

    @Override
    public int compareTo(Node other) {
      return Integer.compare(this.cost, other.cost);
    }
  }
}    //    Main-class-end
```