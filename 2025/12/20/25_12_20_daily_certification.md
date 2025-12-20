# 25_12_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32156 Minimum Spanning Arborescence

[32156번: Minimum Spanning Arborescence](http://boj.ma/32156/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static int arrived = 0; //  r에서 출발해 도달 가능한 정점 개수

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //  정점 개수
    int m = Integer.parseInt(st.nextToken()); //  간선 개수
    int r = Integer.parseInt(st.nextToken()); //  루트

    List<int[]>[] graph = new ArrayList[n + 1];
    boolean[] visited = new boolean[n + 1];
    long[] minIncomings = new long[n + 1];

    long answer = 0;

    for(int i = 1; i <= n; i++) {
      graph[i] = new ArrayList<>();
      minIncomings[i] = Long.MAX_VALUE;
    }

    for(int e = 0; e < m; e++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int cost = Integer.parseInt(st.nextToken());

      graph[v1].add(new int[] {v2, cost});
    }

    dfs(r, graph, visited);

    if(arrived != n) { //  r에서부터 출발해 모든 정점에 도달할 수 없을 경우
      System.out.println(-1);
      return;
    }

    for(int v = 1; v <= n; v++) {
      for(int[] edge : graph[v]) {
        int nv = edge[0];
        long cost = edge[1];

        minIncomings[nv] = Math.min(minIncomings[nv], cost);
      }
    }

    for(int v = 1; v <= n; v++) {
      if(v != r)
        answer += minIncomings[v];
    }

    System.out.println(answer);
  } //  main-end

  private static void dfs(int cur, List<int[]>[] graph, boolean[] visited) {
    visited[cur] = true;
    arrived++;

    for(int[] edge : graph[cur]) {
      if(!visited[edge[0]])
        dfs(edge[0], graph, visited);
    }
  }
} //  Main-class-end
```