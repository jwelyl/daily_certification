# 26_02_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2276 물 채우기

[2276번: 물 채우기](http://boj.ma/2276/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static int answer = 0;

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int m = Integer.parseInt(st.nextToken());
    int n = Integer.parseInt(st.nextToken());

    int[][] map = new int[n][m];
    boolean[][] visited = new boolean[n][m];
    PriorityQueue<Pos> pq = new PriorityQueue<>();

    for(int y = 0; y < n; y++) {
      st = new StringTokenizer(br.readLine());
      for(int x = 0; x < m; x++) {
        map[y][x] = Integer.parseInt(st.nextToken());
        if(y == 0 || y == n - 1 || x == 0 || x == m - 1) {  //  테두리일 경우
          visited[y][x] = true;
          pq.offer(new Pos(y, x, map[y][x]));
        }
      }
    }

    while(!pq.isEmpty()) {
      Pos cur = pq.poll();  //  현재 바깥 테두리 중 가장 낮은 높이를 가진 칸의 정보
      dfs(cur.y, cur.x, cur.h, n, m, map, visited, pq);
    }

    System.out.println(answer);
  } //  main-end

  private static void dfs(int y, int x, int h, int n, int m, int[][] map, boolean[][] visited, PriorityQueue<Pos> pq) {
    for(int d = 0; d < 4; d++) {
      int ny = y + dy[d];
      int nx = x + dx[d];

      if(isIn(ny, nx, n, m) && !visited[ny][nx]) {
        visited[ny][nx] = true;

        if(map[ny][nx] > h) //  안쪽의 바닥의 높이가 더 높을 경우
          pq.offer(new Pos(ny, nx, map[ny][nx]));
        else {  //  바깥쪽의 바닥의 높이가 더 높을 경우
          answer += (h - map[ny][nx]);
          pq.offer(new Pos(ny, nx, h));
        }
      }
    }
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static boolean isIn(int y, int x, int n, int m) {
    return (0 <= y && y < n) && (0 <= x && x < m);
  }

  private static class Pos implements Comparable<Pos> {
    public int y;
    public int x;
    public int h;

    public Pos(int y, int x, int h) {
      this.y = y;
      this.x = x;
      this.h = h;
    }

    @Override
    public int compareTo(Pos other) {
      return Integer.compare(this.h, other.h);
    }
  }
} //  Main-class-end
```