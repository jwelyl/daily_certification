# 26_03_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1119 그래프

[1119번: 그래프](http://boj.ma/1119/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());
    char[][] graph = new char[n + 1][n + 1];
    boolean[] visited = new boolean[n + 1];
    int edgeCnt = 0;
    List<Integer> componentSizeList = new ArrayList<>();
    int sum = 0;

    if (n == 1) {
      System.out.println(0);
      return;
    }

    for (int v1 = 1; v1 <= n; v1++) {
      String input = br.readLine();
      for (int v2 = 1; v2 <= n; v2++) {
        graph[v1][v2] = input.charAt(v2 - 1);
        if (graph[v1][v2] == 'Y')
          edgeCnt++;
      }
    }

    edgeCnt /= 2;

    for (int i = 1; i <= n; i++) {
      if (!visited[i]) {
        visited[i] = true;
        componentSizeList.add(dfs(i, n, graph, visited));
      }
    }

    for (int size : componentSizeList) {
      sum += (size - 1);
      if (size == 1) {
        System.out.println(-1);
        return;
      }
    }

    if (componentSizeList.size() - 1 <= edgeCnt - sum)
      System.out.println(componentSizeList.size() - 1);
    else
      System.out.println(-1);
  } //  main-end

  private static int dfs(int cur, int n, char[][] graph, boolean[] visited) {
    int res = 1;
    for (int next = 1; next <= n; next++) {
      if (graph[cur][next] != 'Y' || visited[next])
        continue;

      visited[next] = true;
      res += dfs(next, n, graph, visited);
    }
    return res;
  }
} //  Main-class-end
```