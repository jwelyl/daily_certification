# 26_02_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17090 미로 탈출하기

[17090번: 미로 탈출하기](http://boj.ma/17090/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
  private static final int ZERO = 0; // 아직 방문 안함.
  private static final int NONE = -1; // 이곳에서 출발하면 탈출 불가.
  private static final int PASS = 1; // 이곳에서 출발하면 탈출 가능.
  private static final int UNKNOWN = 2;	//	방문은 했지만 아직 결정 안난 상태

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    Map<Character, Integer> dirMap = new HashMap<>();
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    char[][] map = new char[n][m];
    int[][] check = new int[n][m];
    int answer = 0;

    dirMap.put('R', 0);
    dirMap.put('D', 1);
    dirMap.put('L', 2);
    dirMap.put('U', 3);

    for (int i = 0; i < n; i++)
      map[i] = br.readLine().toCharArray();

    for (int i = 0; i < n; i++) {
      for (int j = 0; j < m; j++) {
        if (check[i][j] == ZERO) {
          dfs(i, j, n, m, dirMap, map, check);
          if (check[i][j] == PASS)
            answer++;
        } else if (check[i][j] == PASS)
          answer++;
      }
    }

    System.out.println(answer);
  } //  main-end

  private static final int[] dy = { 0, 1, 0, -1 };
  private static final int[] dx = { 1, 0, -1, 0 };

  private static boolean isIn(int y, int x, int n, int m) {
    return (0 <= y && y < n) && (0 <= x && x < m);
  }

  private static int dfs(int y, int x, int n, int m, Map<Character, Integer> dirMap, char[][] map, int[][] check) {
    char ch = map[y][x];
    int ny = y + dy[dirMap.get(ch)];
    int nx = x + dx[dirMap.get(ch)];
    check[y][x] = UNKNOWN;

    if(!isIn(ny, nx, n, m))
      return check[y][x] = PASS;

    if(check[ny][nx] == NONE || check[ny][nx] == UNKNOWN)
      return check[y][x] = NONE;
    else if(check[ny][nx] == PASS)
      return check[y][x] = PASS;
    else
      return check[y][x] = dfs(ny, nx, n, m, dirMap, map, check);
  }
} //  Main-class-end
```