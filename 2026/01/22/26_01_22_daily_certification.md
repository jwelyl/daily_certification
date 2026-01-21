# 26_01_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 34513 룩의 이동

[34513번: 룩의 이동](http://boj.ma/34513/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
  private static final int NONE = Integer.MAX_VALUE;

  private static final char R = 'R';   //  흑 룩
  private static final char K = 'K';   //  백 킹
  private static final char B = 'B';   //  다른 흑 기물
  private static final char W = 'W';   //  다른 백 기물
  private static final char E = '.';   //  빈 칸

  private static final int S = 0;      //  정지 상태

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    int rookY = 0;
    int rookX = 0;
    int kingY = 0;
    int kingX = 0;

    char[][] board = new char[n][n];
    int[][][] dist = new int[n][n][5];

    for(int y = 0; y < n; y++) {
      board[y] = br.readLine().toCharArray();
      for(int x = 0; x < n; x++) {
        if(board[y][x] == R) {
          rookY = y;
          rookX = x;
        } else if(board[y][x] == K) {
          kingY = y;
          kingX = x;
        }
        for(int d = 0; d < 5; d++)
          dist[y][x][d] = NONE;
      }
    }

    System.out.println(bfs(n, rookY, rookX, kingY, kingX, board, dist));
  } //  main-end

  private static int bfs(int n, int rookY, int rookX, int kingY, int kingX, char[][] board, int[][][] dist) {
    Deque<int[]> deque = new ArrayDeque<>();
    int res = NONE;

    dist[rookY][rookX][S] = 0;
    deque.offerLast(new int[] {rookY, rookX, dist[rookY][rookX][S], S}); //  최초에는 정지 상태

    while(!deque.isEmpty()) {
      int[] cur = deque.pollFirst();
      int cy = cur[0];
      int cx = cur[1];  //  현재 위치
      int cc = cur[2];  //  현재 위치까지 오는 최소 이동횟수
      int cd = cur[3];  //  현재 위치에 오기 위한 마지막 이동 방향

      for(int d = 1; d <= 4; d++) {
        int ny = cy + dy[d];
        int nx = cx + dx[d];
        int nc = cc;

        //  다음 칸이 체스판 밖이거나, 흑 기물이면 갈 수 없음
        if(!isIn(ny, nx, n) || board[ny][nx] == B)
          continue;

        if(board[ny][nx] == K) { //  잡으려는 백 킹일 경우
          //  (cy, cd)까지 올때의 방향 그대로 가면 백 킹을 잡을 수 있을 경우 이동 횟수 증가 x, 아닐 경우 이동 횟수 1 증가
          nc = (d == cd) ? cc : cc + 1;

          if(nc < dist[ny][nx][d]) {
            dist[ny][nx][d] = nc;
            if(dist[ny][nx][d] > cc) //  이동 횟수 1 증가할 경우, deque 뒤에 추가
              deque.offerLast(new int[] {ny, nx, dist[ny][nx][d], S});
            else // 이동 횟수 증가하지 않을 경우, deque 앞에 추가
              deque.offerFirst(new int[] {ny, nx, dist[ny][nx][d], S});
          }
        }
        else if(board[ny][nx] == W) { //  다른 백색 기물일 경우
          if(d != cd)
            nc = cc + 1;

          if(nc < dist[ny][nx][d]) {
            dist[ny][nx][d] = nc;

            //  어떤 경우건 백색 기물을 잡기 위해 정지해야 함
            if(dist[ny][nx][d] > cc) //  이동 횟수 1 증가할 경우, deque 뒤에 추가
              deque.offerLast(new int[] {ny, nx, dist[ny][nx][d], S});
            else // 이동 횟수 증가하지 않을 경우, deque 앞에 추가
              deque.offerFirst(new int[] {ny, nx, dist[ny][nx][d], S});
          }
        }
        else {
          if(d != cd)
            nc = cc + 1;

          if(nc < dist[ny][nx][d]) {
            dist[ny][nx][d] = nc;

            if(dist[ny][nx][d] > cc) //  이동 횟수 1 증가할 경우, deque 뒤에 추가
              deque.offerLast(new int[] {ny, nx, dist[ny][nx][d], d});
            else // 이동 횟수 증가하지 않을 경우, deque 앞에 추가
              deque.offerFirst(new int[] {ny, nx, dist[ny][nx][d], d});
          }
        }
      }
    }

    for(int d = 0; d < 5; d++)
      res = Math.min(res, dist[kingY][kingX][d]);

    return res == NONE ? -1 : res;
  }

  private static final int[] dy = {0, 0, 1, 0, -1};
  private static final int[] dx = {0, 1, 0, -1, 0};

  private static boolean isIn(int y, int x, int n) {
    return 0 <= y && y < n && 0 <= x && x < n;
  }
} //  Main-class-end

```