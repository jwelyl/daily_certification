# 26_01_29_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 1600 말이 되고픈 원숭이**

[1600번: 말이 되고픈 원숭이](http://boj.ma/1600/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer st;

  private static int K;
  private static int W;
  private static int H;
  private static int[][] map;
  private static boolean[][][] visited;
  private static int time = 0;

  public static void main(String[] args) throws IOException {
    K = Integer.parseInt(br.readLine());
    st = new StringTokenizer(br.readLine());
    W = Integer.parseInt(st.nextToken());
    H = Integer.parseInt(st.nextToken());

    map = new int[H][W];
    visited = new boolean[K + 1][H][W];

    for (int i = 0; i < H; i++) {
      st = new StringTokenizer(br.readLine());
      for (int j = 0; j < W; j++)
        map[i][j] = Integer.parseInt(st.nextToken());
    }

    if (W == 1 && H == 1)
      System.out.println(0);
    else {
      bfs();
      System.out.println(time);
    }
  }

  private static final int[] dy = { 1, 0, -1, 0, -2, -1, 1, 2, 2, 1, -1, -2 };
  private static final int[] dx = { 0, 1, 0, -1, 1, 2, 2, 1, -1, -2, -2, -1 };

  private static boolean isIn(int y, int x) {
    return (0 <= y && y < H) && (0 <= x && x < W);
  }

  private static void bfs() {
    Queue<Node> queue = new ArrayDeque<>();
    boolean isOk = false;
    visited[K][0][0] = true;
    queue.offer(new Node(0, 0, K));

    OUTER: while (!queue.isEmpty()) {
      int size = queue.size();
      time++;

      for (int i = 0; i < size; i++) {
        Node cur = queue.poll();
        int cy = cur.y;
        int cx = cur.x;
        int cjump = cur.jump;

        for (int d = 0; d < 12; d++) {
          int ny = cy + dy[d];
          int nx = cx + dx[d];
          int njump = cjump;

          if (d >= 4) { // 말처럼 이동하는 경우
            if (cjump == 0) // 말처럼 이동할 수 있는 횟수가 없을 경우
              break;
            njump = cjump - 1; // 점프 가능 횟수 1 감소
          }

          if (!isIn(ny, nx) || map[ny][nx] == 1) // 범위 벗어나거나 장애물일 경우
            continue;

          if (!visited[njump][ny][nx]) { // 장애물 아니고 아직 방문 안했을 경우
            if (ny == H - 1 && nx == W - 1) {
              isOk = true;
              break OUTER;
            }
            visited[njump][ny][nx] = true;
            queue.offer(new Node(ny, nx, njump));
          }
        }
      }
    }

    if (!isOk)
      time = -1;
  }

  private static class Node {
    public int y; // 원숭이 좌표
    public int x;
    public int jump; // 말처럼 뛸 수 있는 횟수

    public Node(int y, int x, int jump) {
      this.y = y;
      this.x = x;
      this.jump = jump;
    }
  }
} //  Main-class-end
```