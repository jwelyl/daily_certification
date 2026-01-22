# 26_01_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 33907 투명 스프레이

[33907번: 투명 스프레이](http://boj.ma/33907/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX = 1_000_000_000;
  private static final int INF = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new java.io.InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    int k = Integer.parseInt(st.nextToken());

    int[][] map = new int[n][m];
    for(int y = 0; y < n; y++) {
      st = new StringTokenizer(br.readLine());
      for(int x = 0; x < m; x++)
        map[y][x] = Integer.parseInt(st.nextToken());
    }

    System.out.println(parametricSearch(n, m, k, map));
  } //  main-end

  private static int parametricSearch(int n, int m, int k, int[][] map) {
    int start = 0;
    int end = MAX;
    int res = INF;

    while(start <= end) {
      int mid = (start + end) / 2;

      if(bfs(n, m, k, map, mid)) {
        res = mid;
        end = mid - 1;
      }
      else
        start = mid + 1;
    }

    return res == INF ? -1 : res;
  }

  private static boolean bfs(int n, int m, int k, int[][] map, int mid) {
    Deque<int[]> deque = new ArrayDeque<>();
    int[][] dist = new int[n][m];
    for(int y = 0; y < n; y++) {
      for(int x = 0; x < m; x++) {
        dist[y][x] = INF;
      }
    }

    deque.offerLast(new int[] {0, 0, 0});

    while(!deque.isEmpty()) {
      int[] cur = deque.pollFirst();
      int cy = cur[0];
      int cx = cur[1];
      int cc = cur[2];  //  (cy, cx)까지 오기 위해 사용한 투명 스프레이 개수

      for(int d = 0; d < 4; d++) {
        int ny = cy + dy[d];
        int nx = cx + dx[d];
        int nc = cc;

        if(isIn(ny, nx, n, m)) {
          if (map[ny][nx] > mid) { //  mid보다 큰 칸은 투명 스프레이 필요함
            nc = cc + 1;

            if(nc > k)  //  k번 넘게 투명 스프레이를 써야 하는 경우
              continue;
          }

          if(ny == n - 1 && nx == m - 1)  //  k번 이내로 투명 스프레이를 사용해서 (n - 1, m - 1)에 도착 가능한 경우
            return true;

          if(nc < dist[ny][nx]) {
            dist[ny][nx] = nc;

            if(dist[ny][nx] == cc)
              deque.offerFirst(new int[] {ny, nx, dist[ny][nx]});
            else
              deque.offerLast(new int[] {ny, nx, dist[ny][nx]});
          }
        }
      }
    }

    return false;
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static boolean isIn(int y, int x, int n, int m) {
    return 0 <= y && y < n && 0 <= x && x < m;
  }
} //  Main-class-end

```