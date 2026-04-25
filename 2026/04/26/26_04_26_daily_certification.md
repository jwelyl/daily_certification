# 26_04_26_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 13016 내 왼손에는 흑염룡이 잠들어 있다**

[13016번: 내 왼손에는 흑염룡이 잠들어 있다](https://www.acmicpc.net/problem/13016)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int n = Integer.parseInt(br.readLine());

    List<Node>[] tree = new ArrayList[n + 1];
    for (int v = 1; v <= n; v++)
      tree[v] = new ArrayList<>();

    for (int e = 0; e < n - 1; e++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int dist = Integer.parseInt(st.nextToken());

      tree[v1].add(new Node(v2, dist));
      tree[v2].add(new Node(v1, dist));
    }

    int[] distFrom1 = computeDistances(1, tree);
    int v1 = getFarthestNode(distFrom1);

    int[] distFromV1ForV2 = computeDistances(v1, tree);
    int v2 = getFarthestNode(distFromV1ForV2);

    int[] dist1 = computeDistances(v1, tree);
    int[] dist2 = computeDistances(v2, tree);

    for (int v = 1; v <= n; v++)
      sb.append(Math.max(dist1[v], dist2[v])).append('\n');

    System.out.print(sb);
  } //  main-end

  // start 정점에서 모든 정점까지의 거리를 구하는 DFS (스택 이용, 재귀 X)
  private static int[] computeDistances(int start, List<Node>[] tree) {
    int n = tree.length - 1;
    int[] dist = new int[n + 1];
    boolean[] visited = new boolean[n + 1];

    int[] stack = new int[n];
    int sp = 0;

    stack[sp++] = start;
    visited[start] = true;
    dist[start] = 0;

    while (sp > 0) {
      int v = stack[--sp];

      for (Node next : tree[v]) {
        int nv = next.vertex;
        int nd = next.dist;
        if (!visited[nv]) {
          visited[nv] = true;
          dist[nv] = dist[v] + nd;
          stack[sp++] = nv;
        }
      }
    }

    return dist;
  }

  // dist 배열에서 가장 거리가 먼 정점의 번호를 반환
  private static int getFarthestNode(int[] dist) {
    int idx = 1;
    int max = dist[1];

    for (int i = 2; i < dist.length; i++) {
      if (dist[i] > max) {
        max = dist[i];
        idx = i;
      }
    }
    return idx;
  }

  private static class Node {
    public int vertex;
    public int dist;

    public Node(int vertex, int dist) {
      this.vertex = vertex;
      this.dist = dist;
    }
  }
} //  Main-class-end
```