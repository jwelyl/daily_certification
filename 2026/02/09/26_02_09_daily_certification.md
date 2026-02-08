# 26_02_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 27068 이미지 보정 작업

[27068번: 이미지 보정 작업](http://boj.ma/27068/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    int k = Integer.parseInt(st.nextToken());   //  최대로 보정할 수 있는 구역 개수
    long x = Long.parseLong(st.nextToken());    //  최대 선명도이자, 보정 후 선명도

    long[][] definitions  = new long[n][m];
    for(int r = 0; r < n; r++) {
      st = new StringTokenizer(br.readLine());
      for(int c = 0; c < m; c++)
        definitions[r][c] = Long.parseLong(st.nextToken());
    }

    System.out.println(parametricSearch(n, m, k, x, definitions));
  }    //    main-end

  //  k개 이하의 선명도를 x로 보정했을때, 모든 서로 인접한 칸끼리의 선명도 차이의 최댓값의 최소
  private static long parametricSearch(int n, int m, int k, long x, long[][] definitions) {
    long start = 0;
    long end = x;
    long res = end;

    while(start <= end) {
      long mid = (start + end) / 2;  //  인접한 칸끼리의 선명도 차이를 mid 이하로 제한

      if(bfs(n, m, k, x, mid, copy(definitions))) {  //  k개 이하만 x로 보정해서, 인접한 칸끼리 mid 이하의 선명도 차이만 존재할 경우
        res = Math.min(res, mid);
        end = mid - 1;
      }
      else
        start = mid + 1;
    }

    return res;
  }

  private static boolean bfs(int n, int m, int k, long x, long mid, long[][] copied) {
    Queue<int[]> queue = new ArrayDeque<>();
    int cnt = 0;  //  보정 횟수

    for(int r = 0; r < n; r++) {
      for(int c = 0; c < m; c++) {
        if(copied[r][c] == x) //  보정할 필요 없는 칸일 경우
          continue;

        for(int d = 0; d < 4; d++) {
          int nr = r + dr[d];
          int nc = c + dc[d];

          //  (nr, nc)와 (r, c)의 차이가 mid 이상이라 (r, c)를 보정해야 할 경우
          //  (nr, nc)를 x로 보정하면 차이가 감소하지 않음
          if(isIn(nr, nc, n, m) && copied[nr][nc] - copied[r][c] > mid) {
            cnt++;  //  보정 횟수 1 증가
            copied[r][c] = x;
            queue.offer(new int[] {r, c});
            break;  //  (r, c)를 보정했으므로 주변칸 더 볼 필요 없음
          }
        }
      }
    }

    if(cnt > k)
      return false;

    while(!queue.isEmpty()) {
      int[] cur = queue.poll();
      int cr = cur[0];
      int cc = cur[1];

      for(int d = 0; d < 4; d++) {
        int nr = cr + dr[d];
        int nc = cc + dc[d];

        //  (nr, nc)칸도 보정해야 할 경우
        if(isIn(nr, nc, n, m) && x - copied[nr][nc] > mid) {
          cnt++;

          if(cnt > k)
            return false;

          copied[nr][nc] = x;
          queue.offer(new int[] {nr, nc});
        }
      }
    }

    return true;
  }

  private static long[][] copy(long[][] definitions) {
    long[][] copied = new long[definitions.length][definitions[0].length];
    for(int r = 0; r < definitions.length; r++)
      System.arraycopy(definitions[r], 0, copied[r], 0, definitions[0].length);

    return copied;
  }

  private static final int[] dr = {0, 1, 0, -1};
  private static final int[] dc = {1, 0, -1, 0};

  private static boolean isIn(int r, int c, int n, int m) {
    return (0 <= r && r < n) && (0 <= c && c < m);
  }
}    //    Main-class-end
```