# 26_04_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16933 벽 부수고 이동하기 3

[16933번: 벽 부수고 이동하기 3](http://boj.ma/16933/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.StringTokenizer;
import java.util.Queue;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    int k = Integer.parseInt(st.nextToken());

    char[][] map = new char[n][m];
    boolean[][][] visited = new boolean[n][m][k + 1]; //    visited[y][x][k] : (y, x)에 벽을 k개 부수고 방문했으면 true

    for(int y = 0; y < n; y++)
      map[y] = br.readLine().toCharArray();

    System.out.println(bfs(n, m, k, map, visited));
  }    //    main-end

  private static int bfs(int n, int m, int k, char[][] map, boolean[][][] visited) {
    if(n == 1 && m == 1)
      return 1;

    Queue<int[]> queue = new ArrayDeque<>();
    visited[0][0][0] = true;
    queue.offer(new int[] {0, 0, 0, 1});    //    (0, 0)에 벽을 0개 부순 상태로 1칸째임

    while(!queue.isEmpty()) {
      int[] cur = queue.poll();
      int cy = cur[0];
      int cx = cur[1];        //    현재 위치 (cy, cx)
      int cw = cur[2];        //    (cy, cx)로 올 때까지 벽을 부순 횟수
      int ccnt = cur[3];      //    (cy, cx)까지 이동한 횟수 (홀수면 낮, 짝수면 밤)

      for(int d = 0; d < 4; d++) {
        int ny = cy + dy[d];
        int nx = cx + dx[d];    //    다음에 갈 위치
        int nw;                 //    (ny, nx)에 갈 때까지 벽을 부순 횟수
        int ncnt = ccnt + 1;    //    (ny, nx)까지 이동한 횟수

        if(!isIn(ny, nx, n, m))    //    범위를 벗어난 경우
          continue;

        if(ny == n - 1 && nx == m - 1)    //    다음 칸이 도착칸일 경우
          return ncnt;    //    도착칸은 벽이 아니므로 무조건 갈 수 있음

        if(map[ny][nx] == '0') {    //    다음 칸이 벽이 아닐 경우
          if(!visited[ny][nx][cw]) {    //    현재 벽 부순 횟수로 방문한 적 없을 경우
            visited[ny][nx][cw] = true;
            queue.offer(new int[] {ny, nx, cw, ncnt});
          }
        }
        else {    //    다음 칸이 벽일 경우
          if(cw < k) {    //    아직 벽을 더 부술 수 있을 경우
            nw = cw + 1;

            if(!visited[ny][nx][nw]) {    //    아직 nw개의 벽을 부순 채 방문한 적이 없을 경우
              if(ccnt % 2 == 1) {	//	현재 낮이라서 바로 (ny, nx)로 벽을 부수고 갈 수 있을 경우
                visited[ny][nx][nw] = true;
                queue.offer(new int[] {ny, nx, nw, ncnt});
              }
              else	//	현재 밤이라서 바로 (ny, nx)로 갈 수 없을 경우, 일단 현재 칸에서 대기
                queue.offer(new int[] {cy, cx, cw, ncnt});
            }
          }
        }
      }
    }

    return -1;    //    (N - 1, M - 1)에 도착하지 못한 경우
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static boolean isIn(int y, int x, int n, int m) {
    return (0 <= y && y < n) && (0 <= x && x < m);
  }
}    //    Main-class-end
```