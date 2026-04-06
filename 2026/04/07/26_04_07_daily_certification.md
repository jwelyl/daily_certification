# 26_04_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17835 면접보는 승범이네

[17835번: 면접보는 승범이네](http://boj.ma/17835/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static final long INF = Long.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());   //    도시 개수
    int m = Integer.parseInt(st.nextToken());   //    도로 개수
    int k = Integer.parseInt(st.nextToken());   //    면접장 개수

    List<Node>[] graph = new ArrayList[n + 1];         //    면접장들로부터 각 도시까지 최단 거리 구하기 위한 역방향 그래프
    long[] dist = new long[n + 1];                     //    dist[i] : 어떤 면접장으로부터 i까지의 최단 거리
    PriorityQueue<Node> pq = new PriorityQueue<>();    //    dijkstra pq

    int farthest = 0;             //    면접장으로부터 가장 먼 도시
    long farthestCost = 0;        //    면접장으로부터 가장 먼 도시까지의 비용

    for (int i = 0; i <= n; i++) {
      dist[i] = INF;
      graph[i] = new ArrayList<>();
    }

    for (int e = 0; e < m; e++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int c = Integer.parseInt(st.nextToken());

      graph[v2].add(new Node(v1, c));    //    역방향 그래프 생성
    }

    st = new StringTokenizer(br.readLine());
    for (int i = 0; i < k; i++) {
      int interview = Integer.parseInt(st.nextToken());
      dist[interview] = 0;

      pq.offer(new Node(interview, dist[interview]));
    }

    dijkstra(graph, dist, pq);

    for (int i = 1; i <= n; i++) {
      if (dist[i] > farthestCost) {
        farthestCost = dist[i];
        farthest = i;
      }
    }

    System.out.println(farthest);
    System.out.println(farthestCost);
  }    //    main-end

  private static void dijkstra(List<Node>[] graph, long[] dist, PriorityQueue<Node> pq) {
    while (!pq.isEmpty()) {
      Node cur = pq.poll();
      int cv = cur.vertex;
      long cc = cur.cost;

      if (dist[cv] < cc) {
        continue;
      }

      for (Node next : graph[cv]) {
        int nv = next.vertex;
        long nc = cc + next.cost;

        if (nc < dist[nv]) {
          dist[nv] = nc;
          pq.offer(new Node(nv, dist[nv]));
        }
      }
    }
  }

  private static class Node implements Comparable<Node> {
    public int vertex;
    public long cost;

    public Node(int vertex, long cost) {
      this.vertex = vertex;
      this.cost = cost;
    }

    @Override
    public int compareTo(Node other) {
      return Long.compare(this.cost, other.cost);
    }
  }
}    //    Main-class-end
```