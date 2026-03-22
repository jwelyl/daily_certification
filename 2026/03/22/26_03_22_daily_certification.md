# 26_03_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12896 스크루지 민호

[12896번: 스크루지 민호](http://boj.ma/12896/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static int root = 1;
  private static int maxDist = 0;
  private static int maxNode = 0;

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());

    List<Integer>[] tree = new ArrayList[n + 1];
    boolean[] visited = new boolean[n + 1];
    for (int v = 0; v <= n; v++)
      tree[v] = new ArrayList<>();

    for (int e = 0; e < n - 1; e++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());

      tree[v1].add(v2);
      tree[v2].add(v1);
    }

    dfs(root, 0, tree, visited);

    root = maxNode;
    maxDist = 0;
    Arrays.fill(visited, false);

    dfs(root, 0, tree, visited);

    System.out.println((maxDist + 1) / 2);
  } //  main-end

  private static void dfs(int v, int dist, List<Integer>[] tree, boolean[] visited) {
    visited[v] = true;

    if (dist > maxDist) {
      maxDist = dist;
      maxNode = v;
    }

    for (int nv : tree[v]) {
      if (!visited[nv])
        dfs(nv, dist + 1, tree, visited);
    }
  }
} //  Main-class-end
```