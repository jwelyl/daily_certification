# 26_02_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1103 게임

[1103번: 게임](http://boj.ma/1103/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int NONE = Integer.MIN_VALUE;
  private static final int INF = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());

    char[][] board = new char[n][m];
    boolean[][] visited = new boolean[n][m];
    long[][] dp = new long[n][m];

    for(int y = 0; y < n; y++) {
      board[y] = br.readLine().toCharArray();
      for(int x = 0; x < m; x++)
        dp[y][x] = NONE;
    }

    dp(0, 0, n, m, board, visited, dp);

    System.out.println(dp[0][0] >= INF ? -1 : dp[0][0]);
  }    //    main-end

  private static long dp(int y, int x, int n, int m, char[][] board, boolean[][] visited, long[][] dp) {
    if(dp[y][x] != NONE)
      return dp[y][x];

    visited[y][x] = true;

    if(board[y][x] == 'H')	//	(y, x)가 구멍이면 더 나아갈 수 없음
      dp[y][x] = 0;
    else {
      dp[y][x] = 1;		//	(y, x)가 구멍이 아니라면 일단 한번은 움직일 수 있음

      for(int d = 0; d < 4; d++) {
        int ny = y + dy[d] * (board[y][x] - '0');
        int nx = x + dx[d] * (board[y][x] - '0');	//	다음에 이동할 칸 (ny, nx)

        if(isIn(ny, nx, n, m)) {
          if(visited[ny][nx]) {	//	이미 방문한 칸을 또 방문하게 되는 경우, 사이클 발생
            dp[y][x] = INF;		//	무한히 움직일 수 있음
            break;
          }

          //	(ny, nx)까지 이동한 후 (ny, nx)에서 최대로 이동할 수 있는 횟수를 더함
          dp[y][x] =	Math.max(dp[y][x], 1L + dp(ny, nx, n, m, board, visited, dp));
        }
      }
    }

    visited[y][x] = false;
    return dp[y][x];
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static boolean isIn(int y, int x, int n, int m) {
    return (0 <= y && y < n) && (0 <= x && x < m);
  }
}    //    Main-class-end
```