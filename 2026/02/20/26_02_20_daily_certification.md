# 26_02_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14325 Rain (Large)

[14325번: Rain (Large)](http://boj.ma/14325/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static int answer = 0;

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for(int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int r = Integer.parseInt(st.nextToken());
      int c = Integer.parseInt(st.nextToken());
      int[][] map = new int[r][c];
      boolean[][] visited = new boolean[r][c];
      PriorityQueue<Pos> pq = new PriorityQueue<>();
      answer = 0;

      for(int y = 0; y < r; y++) {
        st = new StringTokenizer(br.readLine());
        for(int x = 0; x < c; x++) {
          map[y][x] = Integer.parseInt(st.nextToken());
          if(y == 0 || y == r - 1 || x == 0 || x == c - 1)  //  테두리일 경우
            pq.offer(new Pos(y, x, map[y][x])); //  테두리부터 높이가 낮은 순으로 꺼낼 수 있도록 우선순위큐에 저장
        }
      }

      while(!pq.isEmpty()) {
        Pos cur = pq.poll();
        dfs(cur.y, cur.x, cur.height, r, c, map, visited, pq);
      }

      sb.append("Case #").append(tc).append(": ").append(answer).append("\n");
    }

    System.out.print(sb);
  } //  main-end

  private static void dfs(int y, int x, int height, int r, int c, int[][] map, boolean[][] visited, PriorityQueue<Pos> pq) {
    visited[y][x] = true;

    for(int d = 0; d < 4; d++) {
      int ny = y + dy[d];
      int nx = x + dx[d];

      if(isIn(ny, nx, r, c) && !visited[ny][nx]) {
        if(map[ny][nx] > height) {  //  이어지는 바깥쪽보다 높이가 높을 경우
          visited[ny][nx] = true;   //  재방문 안하도록 방문처리
          pq.offer(new Pos(ny, nx, map[ny][nx])); //  바깥쪽 벽으로 처리
        }
        else {  //  이어지는 바깥쪽보다 높이가 낮을 경우
          answer += (height - map[ny][nx]);   //  그 차이만큼 정답에 추가
          pq.offer(new Pos(ny, nx, height));  //  바깥쪽 벽으로 처리 (height만큼 막을 수 있음)
          dfs(ny, nx, height, r, c, map, visited, pq);  //  이어지는 안쪽 찾기
        }
      }
    }
  }

  private static final int[] dy = {1, 0, -1, 0};
  private static final int[] dx = {0, 1, 0, -1};

  private static boolean isIn(int y, int x, int r, int c) {
    return (0 <= y && y < r) && (0 <= x && x < c);
  }

  private static class Pos implements Comparable<Pos> {
    public int y;
    public int x;
    public int height;

    public Pos(int y, int x, int height) {
      this.y = y;
      this.x = x;
      this.height = height;
    }

    //  바깥쪽의 높이가 낮은 칸부터 확인하며
    @Override
    public int compareTo(Pos other) {
      return Integer.compare(this.height, other.height);
    }
  }
} //  Main-class-end
```