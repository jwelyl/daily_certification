# 26_02_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12467 Rain Over Atlantis (Small)

[12467번: Rains Over Atlantis (Small)](http://boj.ma/12467/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for (int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int h = Integer.parseInt(st.nextToken());
      int w = Integer.parseInt(st.nextToken());
      int m = Integer.parseInt(st.nextToken());

      int[][] map = new int[h][w];

      int days = 0;

      for (int y = 0; y < h; y++) {
        st = new StringTokenizer(br.readLine());
        for (int x = 0; x < w; x++)
          map[y][x] = Integer.parseInt(st.nextToken());
      }

      while (!allZero(map, h, w)) {
        simulateOneDay(map, h, w, m);
        days++;
      }

      sb.append("Case #").append(tc).append(": ").append(days).append("\n");
    }

    System.out.print(sb);
  } //  Main-class-end

  private static void simulateOneDay(int[][] map, int h, int w, int m) {
    int[][] waterLevel = computeWaterLevel(map, h, w);
    int[][] erosion = new int[h][w];

    // 침식량 계산
    for (int y = 0; y < h; y++) {
      for (int x = 0; x < w; x++) {

        if (map[y][x] == 0)
          continue;

        int minNeighborWL = waterLevel[y][x];

        for (int d = 0; d < 4; d++) {
          int ny = y + dy[d];
          int nx = x + dx[d];

          if (isIn(ny, nx, h, w))
            minNeighborWL = Math.min(minNeighborWL, waterLevel[ny][nx]);
          else
            minNeighborWL = 0; // 바깥은 water level 0
        }

        if (waterLevel[y][x] > minNeighborWL)
          erosion[y][x] = Math.min(waterLevel[y][x] - minNeighborWL, m);
      }
    }

    // 동시에 침식
    for (int y = 0; y < h; y++) {
      for (int x = 0; x < w; x++)
        map[y][x] = Math.max(0, map[y][x] - erosion[y][x]);
    }
  }

  private static int[][] computeWaterLevel(int[][] map, int h, int w) {
    int[][] waterLevel = new int[h][w];
    boolean[][] visited = new boolean[h][w];
    PriorityQueue<Node> pq = new PriorityQueue<>();

    // 경계 삽입
    for (int y = 0; y < h; y++) {
      for (int x = 0; x < w; x++) {
        if (y == 0 || y == h - 1 || x == 0 || x == w - 1) {
          pq.offer(new Node(y, x, map[y][x]));
          visited[y][x] = true;
        }
      }
    }

    while (!pq.isEmpty()) {
      Node cur = pq.poll();

      waterLevel[cur.y][cur.x] = cur.height;

      for (int d = 0; d < 4; d++) {
        int ny = cur.y + dy[d];
        int nx = cur.x + dx[d];

        if (isIn(ny, nx, h, w) && !visited[ny][nx]) {
          visited[ny][nx] = true;
          int newHeight = Math.max(map[ny][nx], cur.height);
          pq.offer(new Node(ny, nx, newHeight));
        }
      }
    }

    return waterLevel;
  }

  private static boolean allZero(int[][] map, int h, int w) {
    for (int y = 0; y < h; y++) {
      for (int x = 0; x < w; x++) {
        if (map[y][x] > 0)
          return false;
      }
    }

    return true;
  }

  private static final int[] dy = {1, 0, -1, 0};
  private static final int[] dx = {0, 1, 0, -1};

  private static boolean isIn(int y, int x, int h, int w) {
    return (0 <= y && y < h) && (0 <= x && x < w);
  }

  private static class Node implements Comparable<Node> {
    public int y;
    public int x;
    public int height;

    public Node(int y, int x, int height) {
      this.y = y;
      this.x = x;
      this.height = height;
    }

    @Override
    public int compareTo(Node o) {
      return Integer.compare(this.height, o.height);
    }
  }
} //  Main-class-end

```