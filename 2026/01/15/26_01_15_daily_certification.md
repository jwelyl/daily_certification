# 26_01_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16724 피리 부는 사나이

[16724번: 피리 부는 사나이](http://boj.ma/16724/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
  private static int answer = 0;    //    사이클 갯수

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());

    List<Integer>[] graph; graph = new ArrayList[n * m];
    boolean[] visited = new boolean[n * m];
    int[] parents = new int[n * m];
    boolean[] finished = new boolean[n * m];

    for(int v = 0; v < n * m; v++)
      graph[v] = new ArrayList<>();

    for(int y = 0; y < n; y++) {
      String input = br.readLine();
      for(int x = 0; x < m; x++) {
        char dir = input.charAt(x);
        int curV = m * y + x;

        int ny = (dir == 'L' || dir == 'R') ? y : (dir == 'U' ? y - 1 : y + 1);
        int nx = (dir == 'U' || dir == 'D') ? x : (dir == 'L' ? x - 1 : x + 1);
        int nextV = m * ny + nx;

        graph[curV].add(nextV);
      }
    }

    for(int y = 0; y < n; y++) {
      for(int x = 0; x < m; x++) {
        int vertex = m * y + x;

        if(!visited[vertex])
          dfs(vertex, graph, visited, parents, finished);
      }
    }

    System.out.println(answer);
  }    //    main-end

  private static void dfs(int cur, List<Integer>[] graph, boolean[] visited, int[] parents, boolean[] finished) {
    visited[cur] = true;

    for(int next : graph[cur]) {
      if(!visited[next]) {
        parents[next] = cur;
        dfs(next,  graph, visited, parents, finished);
      }
      else if(!finished[next])
        makeCycle(cur, next, parents);
    }

    finished[cur] = true;
  }

  private static void makeCycle(int cur, int next, int[] parents) {
    if(cur == next) {
      answer++;
      return;
    }

    int prev = parents[cur];
    makeCycle(prev, next, parents);
  }
}    //    Main-class-end
```