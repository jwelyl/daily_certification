# 25_11_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2820 자동차 공장

[2820번: 자동차 공장](http://boj.ma/2820/t)

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

  private static int order = 0;

  public static void main(String[] args) throws IOException {
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //  직원 수
    int m = Integer.parseInt(st.nextToken()); //  월급 변화 수
    long[] salaries = new long[n + 1];          //  직원 별 초기 월급
    long[] segtree = new long[n * 4];
    long[] lazy = new long[n * 4];
    int[] leftArr = new int[n + 1];
    int[] rightArr = new int[n + 1];

    List<Integer>[] tree = new ArrayList[n + 1];

    for (int v = 1; v <= n; v++)
      tree[v] = new ArrayList<>();

    salaries[1] = Integer.parseInt(br.readLine());
    for(int v = 2; v <= n; v++){
      st = new StringTokenizer(br.readLine());
      salaries[v] = Integer.parseInt(st.nextToken());   //  v의 초기 월급
      int superior = Integer.parseInt(st.nextToken());  //  v의 직속 상관
      tree[superior].add(v);
    }

    dfs(1, tree, leftArr, rightArr);

    for(int v = 1; v <= n; v++)
      update(1, 1, n, segtree, lazy, leftArr[v], leftArr[v], salaries[v] );

    for(int q = 0; q < m; q++){
      st = new StringTokenizer(br.readLine());
      char opt = st.nextToken().charAt(0);

      if(opt == 'p') {
        int superior = Integer.parseInt(st.nextToken());
        int diff = Integer.parseInt(st.nextToken());
        update(1, 1, n, segtree, lazy, leftArr[superior] + 1, rightArr[superior], diff);
      }
      else {
        int employee = Integer.parseInt(st.nextToken());
        sb.append(query(1, 1, n, segtree, lazy, leftArr[employee], leftArr[employee])).append("\n");
      }
    }

    System.out.print(sb);
  } //	main-end

  private static void dfs(int v, List<Integer>[] tree, int[] leftArr, int[] rightArr) {
    leftArr[v] = ++order;
    for (int child : tree[v])
      dfs(child, tree, leftArr, rightArr);
    rightArr[v] = order;
  }

  private static void lazyUpdate(int node, int start, int end, long[] lazy, long[] segtree) {
    if(lazy[node] != 0) {
      segtree[node] += (end - start + 1) * lazy[node];

      if(start != end) {
        lazy[2 * node] += lazy[node];
        lazy[2 * node + 1] += lazy[node];
      }

      lazy[node] = 0;
    }
  }

  private static void update(int node, int start, int end, long[] segtree, long[] lazy, int left, int right, long diff) {
    lazyUpdate(node, start, end, lazy, segtree);
    if(start > right || end < left)
      return;

    if(left <= start && end <= right) {
      segtree[node] += diff * (end - start + 1);
      if(start != end) {
        lazy[2 * node] += diff;
        lazy[2 * node + 1] += diff;
      }

      return;
    }

    int mid = (start + end) / 2;
    update(2 * node, start, mid, segtree, lazy, left, right, diff);
    update(2 * node + 1, mid + 1, end, segtree, lazy, left, right, diff);
  }

  private static long query(int node, int start, int end, long[] segtree, long[] lazy, int left, int right) {
    lazyUpdate(node, start, end, lazy, segtree);

    if(left > end || right < start)
      return 0;

    if(left <= start && end <= right)
      return segtree[node];

    int mid = (start + end) / 2;
    return query(2 * node, start, mid, segtree, lazy, left, right) + query(2 * node + 1, mid + 1, end, segtree, lazy, left, right);
  }
} //	Main-class-end
```