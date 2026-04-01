# 26_04_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20926 얼음 미로

[20926번: 얼음 미로](http://boj.ma/20926/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.PriorityQueue;

public class Main {
  private static final int INF = 9 * 500 * 500 + 1;
  private static final char SRC = 'T';
  private static final char DST = 'E';

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int w = Integer.parseInt(st.nextToken());
    int h = Integer.parseInt(st.nextToken());

    int sy = -1;
    int sx = -1;
    int ey = -1;
    int ex = -1;

    char[][] map = new char[h][w];
    for(int y = 0; y < h; y++) {
      map[y] = br.readLine().toCharArray();
      for(int x = 0; x < w; x++) {
        if(map[y][x] == SRC) {
          sy = y;
          sx = x;
        }
        else if(map[y][x] == DST) {
          ey = y;
          ex = x;
        }
      }
    }

    System.out.println(dijkstra(h, w, sy, sx, ey, ex, map));
  }    //    main-end

  private static int dijkstra(int h, int w, int sy, int sx, int ey, int ex, char[][] map) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    int[] dist = new int[h * w];
    int src = sy * w + sx;
    int dst = ey * w + ex;

    Arrays.fill(dist, INF);

    pq.offer(new Node(src, 0));

    while(!pq.isEmpty()) {
      Node cur = pq.poll();
      int cv = cur.vertex;
      int cy = cv / w;
      int cx = cv % w;
      int cc = cur.cost;

      if(dist[cv] < cc)
        continue;

      //    (cy, cx)에서 이동할 수 있는 칸 찾기
      for(int d = 0; d < 4; d++) {
        int ncost = 0;    //    d 방향으로 미끄러져서 도착할 수 있는 칸까지 거리

        for(int k = 1; ; k++) {
          int ny = cy + dy[d] * k;
          int nx = cx + dx[d] * k;    //    d 방향으로 미끄러져서 도착한 칸
          int nny = ny + dy[d];
          int nnx = nx + dx[d];       //    그 다음 칸

          //    도착한 칸이 절벽이거나, 구멍이거나 바위일 경우
          if(!((0 <= ny && ny < h) && (0 <= nx && nx < w)) || map[ny][nx] == 'H' || map[ny][nx] == 'R')
            break;

          if(map[ny][nx] == DST) {    //    출구에 도달할 경우
            if(cc + ncost < dist[dst]) {    //    출구까지 최단거리가 갱신될 경우
              dist[dst] = cc + ncost;
              pq.offer(new Node(dst, dist[dst]));
            }

            break;
          }

          //    d 방향으로 가면 절벽으로 빠질 경우
          if(!((0 <= nny && nny < h) && (0 <= nnx && nnx < w)))
            break;

          ncost += (map[ny][nx] == SRC || map[ny][nx] == DST) ? 0 : map[ny][nx] - '0';

          //    바위에 의해 멈출 수 있는 칸일 경우
          if(map[nny][nnx] == 'R') {
            int nv = ny * w + nx;

            if(cc + ncost < dist[nv]) {
              dist[nv] = cc + ncost;
              pq.offer(new Node(nv, dist[nv]));
            }
          }
        }
      }
    }

    return dist[dst] == INF ? -1 : dist[dst];
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static class Node implements Comparable<Node> {
    public int vertex;
    public int cost;

    public Node(int vertex, int cost) {
      this.vertex = vertex;
      this.cost = cost;
    }

    @Override
    public int compareTo(Node other) {
      return Integer.compare(this.cost, other.cost);
    }
  }
}    //    Main-class-end
```