# 26_04_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1368 물대기

[](http://boj.ma/1368/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    List<Edge> edges = new ArrayList<>();
    int n = Integer.parseInt(br.readLine());   //    논 개수

    int[] parents = new int[n + 1];
    for(int v = 1; v <= n; v++)
      parents[v] = v;

    for(int v = 1; v <= n; v++) {
      int cost = Integer.parseInt(br.readLine());
      edges.add(new Edge(0, v, cost));
    }

    for(int v1 = 1; v1 <= n; v1++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      for(int v2 = 1; v2 <= n; v2++) {
        int cost = Integer.parseInt(st.nextToken());

        if(v1 < v2)
          edges.add(new Edge(v1, v2, cost));
      }
    }

    System.out.println(kruskal(n, edges, parents));
  }    //    main-end

  private static int kruskal(int n, List<Edge> edges, int[] parents) {
    int mstCost = 0;
    int edgeCnt = 0;

    Collections.sort(edges);

    for(Edge edge : edges) {
      int v1 = edge.v1;
      int v2 = edge.v2;
      int cost = edge.cost;

      int v1DS = find(v1, parents);
      int v2DS = find(v2, parents);

      if(v1DS != v2DS) {
        union(v1DS, v2DS, parents);
        mstCost += cost;
        edgeCnt++;
      }

      if(edgeCnt == n)
        break;
    }

    return mstCost;
  }

  private static int find(int v, int[] parents) {
    return parents[v] == v ? v : (parents[v] = find(parents[v], parents));
  }

  private static void union(int v1, int v2, int[] parents) {
    parents[v2] = v1;
  }

  private static class Edge implements Comparable<Edge> {
    public int v1;
    public int v2;
    public int cost;

    public Edge(int v1, int v2, int cost) {
      this.v1 = v1;
      this.v2 = v2;
      this.cost = cost;
    }

    @Override
    public int compareTo(Edge other) {
      return Integer.compare(this.cost, other.cost);
    }
  }
}    //    Main-class-end
```