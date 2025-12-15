# 25_12_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18321 Wormhole Sort

[18321번: Wormhole Sort](http://boj.ma/18321/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());

    int[] parents = new int[n + 1];
    int[] positions = new int[n + 1]; //  i번째 소의 위치
    int[][] edges = new int[m][3];
    int maxW = 0;
    int diffCnt = 0;
    st = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++) {
      positions[i] = Integer.parseInt(st.nextToken());
      if(positions[i] != i)
        diffCnt++;
    }

    if(diffCnt == 0) {
      System.out.println(-1);
      return;
    }

    for(int i = 0; i < m; i++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int w = Integer.parseInt(st.nextToken());
      edges[i][0] = v1;
      edges[i][1] = v2;
      edges[i][2] = w;
      maxW = Math.max(maxW, w);
    }

    System.out.println(parametricSearch(n, m, maxW, parents, positions, edges));
  } //  main-end

  private static int parametricSearch(int n, int m, int maxW, int[] parents, int[] positions, int[][] edges) {
    int start = 1;
    int end = maxW;
    int res = 1;

    while(start <= end) {
      int mid = (start + end) / 2;
      boolean ok = true;
      reset(n, parents);

      for(int i = 0; i < m; i++) {
        int v1 = edges[i][0];
        int v2 = edges[i][1];
        int w = edges[i][2];

        if(w < mid)
          continue;

        union(v1, v2, parents);
      }

      for(int i = 1; i <= n; i++) {
        if(find(i, parents) != find(positions[i], parents)) {
          ok = false;
          break;
        }
      }

      if(ok) {
        res = mid;
        start = mid + 1;
      }
      else
        end = mid - 1;
    }

    return res;
  }

  private static int find(int x, int[] parents) {
    return x == parents[x] ? x : (parents[x] = find(parents[x], parents));
  }

  private static void union(int x, int y, int[] parents) {
    x = find(x, parents);
    y = find(y, parents);

    if(x != y)
      parents[x] = y;
  }

  private static void reset(int n, int[] parents) {
    for(int i = 1; i <= n; i++)
      parents[i] = i;
  }
} //  Main-class-end
```