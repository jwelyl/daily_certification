# 26_03_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11112 **Eight puzzle**

[11112번: Eight puzzle](http://boj.ma/11112/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Queue;
import java.util.Set;

public class Main {
  private static final char EMPTY = '#';
  private static final String TARGET = "12345678#";

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    Map<String, Integer> distMap = new HashMap<>();

    bfs(distMap);

    int t = Integer.parseInt(br.readLine());

    for (int tc = 0; tc < t; tc++) {
      char[][] board = new char[3][3];
      String init;
      int res;

      br.readLine();
      for (int y = 0; y < 3; y++)
        board[y] = br.readLine().toCharArray();

      init = boardToString(board);
      res = distMap.getOrDefault(init, -1);

      sb.append(res == -1 ? "impossible" : res).append("\n");
    }

    System.out.print(sb);
  }   //  main-end

  private static void bfs(Map<String, Integer> distMap) {
    Queue<String> queue = new ArrayDeque<>();
    Set<String> visited = new HashSet<>();

    int cnt = 0;
    distMap.put(TARGET, 0);
    visited.add(TARGET);
    queue.offer(TARGET);

    while (!queue.isEmpty()) {
      int size = queue.size();

      for (int i = 0; i < size; i++) {
        String cur = queue.poll();
        for (int d = 0; d < 4; d++) {
          String next = move(cur, d);

          if (!visited.contains(next)) {
            distMap.put(next, cnt + 1);
            visited.add(next);
            queue.offer(next);
          }
        }
      }

      cnt++;
    }
  }

  private static String move(String cur, int dir) {
    StringBuilder sb = new StringBuilder();
    char[][] board = new char[3][3];

    for (int y = 0; y < 3; y++) {
      for (int x = 0; x < 3; x++)
        board[y][x] = cur.charAt(y * 3 + x);
    }

    if (dir == 0) {  //  오른쪽으로 밀기
      LOOP:
      for (int y = 0; y < 3; y++) {
        for (int x = 2; x >= 1; x--) {
          if (board[y][x] == EMPTY) {
            board[y][x] = board[y][x - 1];
            board[y][x - 1] = EMPTY;
            break LOOP;
          }
        }
      }
    } else if (dir == 1) { //  아래로 밀기
      LOOP:
      for (int x = 0; x < 3; x++) {
        for (int y = 2; y >= 1; y--) {
          if (board[y][x] == EMPTY) {
            board[y][x] = board[y - 1][x];
            board[y - 1][x] = EMPTY;
            break LOOP;
          }
        }
      }
    } else if (dir == 2) { //  왼쪽으로 밀기
      LOOP:
      for (int y = 0; y < 3; y++) {
        for (int x = 0; x < 2; x++) {
          if (board[y][x] == EMPTY) {
            board[y][x] = board[y][x + 1];
            board[y][x + 1] = EMPTY;
            break LOOP;
          }
        }
      }
    } else {  //  위로 밀기
      LOOP:
      for (int x = 0; x < 3; x++) {
        for (int y = 0; y < 2; y++) {
          if (board[y][x] == EMPTY) {
            board[y][x] = board[y + 1][x];
            board[y + 1][x] = EMPTY;
            break LOOP;
          }
        }
      }
    }

    for (int y = 0; y < 3; y++) {
      for (int x = 0; x < 3; x++)
        sb.append(board[y][x]);
    }

    return sb.toString();
  }

  private static String boardToString(char[][] board) {
    StringBuilder sb = new StringBuilder();
    for (int y = 0; y < 3; y++) {
      for (int x = 0; x < 3; x++)
        sb.append(board[y][x]);
    }

    return sb.toString();
  }
}   //  Main-class-end
```