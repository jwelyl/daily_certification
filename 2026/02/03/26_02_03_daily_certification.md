# 26_02_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2211 네트워크 복구

[2211번: 네트워크 복구](http://boj.ma/2211/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static final int INF = Integer.MAX_VALUE;
  private static final int NONE = -1;

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); // 컴퓨터 개수
    int m = Integer.parseInt(st.nextToken()); // 회선 개수

    List<Edge>[] graph = new ArrayList[n + 1];
    int[] dist = new int[n + 1];
    int[] prevs = new int[n + 1];
    for (int v = 0; v <= n; v++) {
      graph[v] = new ArrayList<>();
      dist[v] = INF;
      prevs[v] = NONE;
    }

    for (int e = 0; e < m; e++) {
      int v1;
      int v2;
      int cost;
      st = new StringTokenizer(br.readLine());
      v1 = Integer.parseInt(st.nextToken());
      v2 = Integer.parseInt(st.nextToken());
      cost = Integer.parseInt(st.nextToken());

      graph[v1].add(new Edge(v2, cost));
      graph[v2].add(new Edge(v1, cost));
    }

    dijkstra(graph, dist, prevs);

    sb.append(n - 1).append("\n");
    track(n, prevs, sb);

    System.out.print(sb);
  } //  main-end

  private static void dijkstra(List<Edge>[] graph, int[] dist, int[] prevs) {
    PriorityQueue<Edge> pq = new PriorityQueue<>();
    dist[1] = 0;
    pq.offer(new Edge(1, 0));

    while (!pq.isEmpty()) {
      Edge cur = pq.poll();
      int curVertex = cur.vertex;
      int curCost = cur.cost;

      if (dist[curVertex] < curCost) {
        continue;
      }

      for (Edge next : graph[curVertex]) {
        int nextVertex = next.vertex;
        int nextCost = curCost + next.cost;

        if (nextCost < dist[nextVertex]) {
          dist[nextVertex] = nextCost;
          prevs[nextVertex] = curVertex;
          pq.offer(new Edge(nextVertex, nextCost));
        }
      }
    }
  }

  private static void track(int n, int[] prevs, StringBuilder sb) {
    boolean[] visited = new boolean[n + 1];

    for (int vertex = 2; vertex <= n; vertex++) {
      int cur = vertex;

      while (true) {
        if (prevs[cur] == NONE || visited[cur])
          break;

        sb.append(cur).append(" ").append(prevs[cur]).append("\n");
        visited[cur] = true;
        cur = prevs[cur];
      }
    }
  }

  private static class Edge implements Comparable<Edge> {
    public int vertex;
    public int cost;

    public Edge(int vertex, int cost) {
      this.vertex = vertex;
      this.cost = cost;
    }

    @Override
    public int compareTo(Edge other) {
      return Integer.compare(this.cost, other.cost);
    }
  }
} //  Main-class-end

```