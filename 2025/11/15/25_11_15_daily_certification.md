# 25_11_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4792 **레드 블루 스패닝 트리**

[4792번: 레드 블루 스패닝 트리](http://boj.ma/4792/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();

  private static int[] parents;
  private static final List<int[]> BLUE_EDGES = new ArrayList<>();
  private static final List<int[]> RED_EDGES = new ArrayList<>();

  public static void main(String[] args) throws IOException {
    while(true) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      //  정점 개수
      int n = Integer.parseInt(st.nextToken());
      //  간선 개수
      int m = Integer.parseInt(st.nextToken());
      //  요구하는 파란 간선 개수
      int k = Integer.parseInt(st.nextToken());

      if(n == 0 && m == 0 && k == 0) {
        System.out.print(sb);
        break;
      }

      BLUE_EDGES.clear();
      RED_EDGES.clear();
      parents = new int[n + 1];
      for(int i = 1; i <= n; i++)
        parents[i] = i;

      for(int e = 0; e < m; e++) {
        st = new StringTokenizer(br.readLine());
        char color = st.nextToken().charAt(0);
        int vertex1 = Integer.parseInt(st.nextToken());
        int vertex2 = Integer.parseInt(st.nextToken());

        if(color == 'B')
          BLUE_EDGES.add(new int[] {vertex1, vertex2});
        else
          RED_EDGES.add(new int[] {vertex1, vertex2});
      }

      //  빨간 간선 먼저 사용해서 MST 구축해보기
      int mstEdgeCount = 0;
      //  이때 필요한 파란 간선 수, 즉 MST를 구축하기 위해 필요한 최소한의 파란 간선 수
      int blueEdgeCount = 0;

      for(int[] redEdge : RED_EDGES) {
        int v1 = redEdge[0];
        int v2 = redEdge[1];

        if(find(v1) != find(v2)) {
          union(v1, v2);
          mstEdgeCount++;
        }

        if(mstEdgeCount == n - 1)
          break;
      }

      //  빨간 간선만으로는 부족할 경우
      if(mstEdgeCount < n - 1) {
        for(int[] blueEdge : BLUE_EDGES) {
          int v1 = blueEdge[0];
          int v2 = blueEdge[1];

          if (find(v1) != find(v2)) {
            union(v1, v2);
            mstEdgeCount++;
            blueEdgeCount++;
          }

          if(mstEdgeCount == n - 1)
            break;
        }
      }

      //  파란 간선을 최소한으로 사용해도 k개보다 더 필요할 경우
      if(blueEdgeCount > k) {
        sb.append("0\n");
        continue;
      }

      //  파란 간선 먼저 사용해서 MST 구축
      mstEdgeCount = 0;
      //  그때 필요한 파란 간선 수
      blueEdgeCount = 0;
      for(int i = 1; i <= n; i++)
        parents[i] = i;

      for(int[] blueEdge : BLUE_EDGES) {
        int v1 = blueEdge[0];
        int v2 = blueEdge[1];

        if(find(v1) != find(v2)) {
          union(v1, v2);
          mstEdgeCount++;
          blueEdgeCount++;
        }

        if(mstEdgeCount == n - 1)
          break;
      }

      //  파란 간선을 최대로 사용했음에도 k개 보다 적게 사용될 경우
      if(blueEdgeCount < k) {
        sb.append("0\n");
        continue;
      }

      sb.append("1\n");
    }
  } //  main-end

  private static int find(int v) {
    return v == parents[v] ? v : find(parents[v]);
  }

  private static void union(int v1, int v2) {
    v1 = find(v1);
    v2 = find(v2);

    if(v1 != v2)
      parents[v2] = v1;
  }
} //  Main-class-end
```