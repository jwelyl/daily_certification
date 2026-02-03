# 26_02_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5542 JOI 국가의 행사

[5542번: JOI 국가의 행사](http://boj.ma/5542/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
  private static final int INF = Integer.MAX_VALUE;

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    int k = Integer.parseInt(st.nextToken());
    int q = Integer.parseInt(st.nextToken());

    List<int[]>[] graph = new ArrayList[n + 1];
    List<Integer> parades = new ArrayList<>();
    List<Edge> edges = new ArrayList<>();
    int[] distanceArr = new int[n + 1];

    Set<Integer>[] nodeCheck = new HashSet[n + 1];  // nodeCheck[node] = 해당 노드가 출발/도착지인 사람들
    int[] parents = new int[n + 1];
    int[] rankSize = new int[n + 1];
    int[] answerArr = new int[n + 1];

    int[] tempU = new int[m];
    int[] tempV = new int[m];

    for (int v = 0; v <= n; v++) {
      parents[v] = v;
      rankSize[v] = 1;
      distanceArr[v] = INF;
      graph[v] = new ArrayList<>();
      nodeCheck[v] = new HashSet<>();
    }

    for (int i = 0; i < m; i++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int cost = Integer.parseInt(st.nextToken());

      graph[v1].add(new int[] {v2, cost});
      graph[v2].add(new int[] {v1, cost});

      tempU[i] = v1;
      tempV[i] = v2;
    }

    for (int i = 0; i < k; i++)
      parades.add(Integer.parseInt(br.readLine()));

    dijkstra(graph, parades, distanceArr);

    // 간선에 축제와의 최소 거리 계산
    for (int i = 0; i < m; i++) {
      int v1 = tempU[i];
      int v2 = tempV[i];
      int paradeDist = Math.min(distanceArr[v1], distanceArr[v2]);
      edges.add(new Edge(v1, v2, paradeDist));
    }

    // 축제와의 거리 내림차순
    edges.sort((a, b) -> b.dist - a.dist);

    // 사람 정보 저장
    for (int i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());
      int from = Integer.parseInt(st.nextToken());
      int to = Integer.parseInt(st.nextToken());
      nodeCheck[from].add(i);
      nodeCheck[to].add(i);
    }

    // 크루스칼 역순 + 유니온
    for (Edge edge : edges) {
      int v1 = find(edge.v1, parents);
      int v2 = find(edge.v2, parents);

      if (v1 == v2)
        continue;

      int bigger;
      int smaller;
      if (rankSize[v1] < rankSize[v2]) {
        bigger = v2;
        smaller = v1;
      } else {
        bigger = v1;
        smaller = v2;
      }

      for (int human : nodeCheck[smaller]) {
        if (nodeCheck[bigger].contains(human))
          answerArr[human] = edge.dist;
        nodeCheck[bigger].add(human);
      }

      union(v1, v2, parents, rankSize);
    }

    for (int i = 0; i < q; i++)
      sb.append(answerArr[i]).append('\n');

    System.out.print(sb);
  } //  main-end

  private static int find(int u, int[] parents) {
    return parents[u] == u ? u : (parents[u] = find(parents[u], parents));
  }

  private static void union(int v1, int v2, int[] parents, int[] rankSizeArr) {
    v1 = find(v1, parents);
    v2 = find(v2, parents);

    if (v1 == v2)
      return;

    if (rankSizeArr[v1] < rankSizeArr[v2])
      parents[v1] = v2;
    else {
      parents[v2] = v1;
      if (rankSizeArr[v1] == rankSizeArr[v2])
        rankSizeArr[v1]++;
    }
  }

  // Multisource Dijkstra
  private static void dijkstra(List<int[]>[] graph, List<Integer> parades, int[] distanceArr) {
    PriorityQueue<Node> pq = new PriorityQueue<>();

    for (int parade : parades) {
      distanceArr[parade] = 0;
      pq.offer(new Node(parade, distanceArr[parade]));
    }

    while (!pq.isEmpty()) {
      Node cur = pq.poll();
      int cv = cur.vertex;
      int cc = cur.cost;

      if (cc > distanceArr[cv])
        continue;

      for (int[] edge : graph[cv]) {
        int nv = edge[0];
        int nc = cc + edge[1];

        if (nc < distanceArr[nv]) {
          distanceArr[nv] = nc;
          pq.offer(new Node(nv, distanceArr[nv]));
        }
      }
    }
  }

  private static class Edge {
    public int v1;
    public int v2;

    public int dist;

    public Edge(int v1, int v2, int dist) {
      this.v1 = v1;
      this.v2 = v2;
      this.dist = dist;
    }
  }

  private static class Node implements Comparable<Node> {
    public int vertex;
    public int cost;

    public Node(int vertex, int cost) {
      this.vertex = vertex;
      this.cost = cost;
    }

    @Override
    public int compareTo(Node other) {
      return Integer.compare(this.cost, other.cost);
    }
  }
} //  Main-class-end

```