# 25_12_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14288 회사 문화 4

[14288번: 회사 문화 4](http://boj.ma/14288/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //  직원 수
    int m = Integer.parseInt(st.nextToken()); //  쿼리 수

    long[] segtree1 = new long[n * 4];  // 상사 -> 부하 (구간 업데이트 + 구간 조회)
    long[] segtree2 = new long[n * 4];  // 부하 -> 상사 (점 업데이트 + 구간 조회)
    long[] lazy = new long[n * 4];      // segtree1용 lazy
    int[] leftArr = new int[n + 1];
    int[] rightArr = new int[n + 1];

    List<Integer>[] tree = new ArrayList[n + 1];
    int dir = 1;  // 1일 경우 상사 -> 부하, -1일 경우 부하 -> 상사

    for (int v = 1; v <= n; v++)
      tree[v] = new ArrayList<>();

    st = new StringTokenizer(br.readLine());
    for (int i = 1; i <= n; i++) {
      int superior = Integer.parseInt(st.nextToken());
      if (superior != -1)
        tree[superior].add(i);
    }

    dfs(1, tree, leftArr, rightArr);

    for (int q = 0; q < m; q++) {
      st = new StringTokenizer(br.readLine());
      int opt = Integer.parseInt(st.nextToken());

      if (opt == 1) {
        int employee = Integer.parseInt(st.nextToken());
        int diff = Integer.parseInt(st.nextToken());

        if (dir == 1) //  상사 -> 부하 : employee의 서브트리 전체 칭찬
          update(1, 1, n, segtree1, lazy, leftArr[employee], rightArr[employee], diff);
        else  // 부하 -> 상사 : employee가 칭찬의 출발점 (부하 중 한 명이 i를 칭찬)
          update(1, 1, n, segtree2, leftArr[employee], diff);
      }
      else if (opt == 2) {
        int employee = Integer.parseInt(st.nextToken());
        sb.append(query(1, 1, n, segtree1, lazy, leftArr[employee], leftArr[employee]) + query(1, 1, n, segtree2, leftArr[employee], rightArr[employee])).append("\n");
      }
      else
        dir *= -1; // 칭찬 방향 전환
    }

    System.out.print(sb);
  } //	main-end

  private static int order = 0;

  private static void dfs(int v, List<Integer>[] tree, int[] leftArr, int[] rightArr) {
    leftArr[v] = ++order;
    for (int child : tree[v])
      dfs(child, tree, leftArr, rightArr);
    rightArr[v] = order;
  }

  //  상사 -> 부하 (lazy 구간 업데이트 + 구간 쿼리)
  private static void lazyUpdate(int node, int start, int end, long[] lazy, long[] segtree) {
    if (lazy[node] != 0) {
      segtree[node] += (end - start + 1L) * lazy[node];

      if (start != end) {
        lazy[2 * node] += lazy[node];
        lazy[2 * node + 1] += lazy[node];
      }

      lazy[node] = 0;
    }
  }

  private static void update(int node, int start, int end, long[] segtree, long[] lazy, int left, int right, long diff) {
    lazyUpdate(node, start, end, lazy, segtree);

    if (start > right || end < left)
      return;

    if (left <= start && end <= right) {
      segtree[node] += diff * (end - start + 1L);
      if (start != end) {
        lazy[2 * node] += diff;
        lazy[2 * node + 1] += diff;
      }
      return;
    }

    int leftChild = 2 * node;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    update(leftChild, start, mid, segtree, lazy, left, right, diff);
    update(rightChild, mid + 1, end, segtree, lazy, left, right, diff);
    segtree[node] = segtree[leftChild] + segtree[rightChild];
  }

  private static long query(int node, int start, int end, long[] segtree, long[] lazy, int left, int right) {
    lazyUpdate(node, start, end, lazy, segtree);

    if (left > end || right < start)
      return 0;

    if (left <= start && end <= right)
      return segtree[node];

    int leftChild = 2 * node;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;
    return query(leftChild, start, mid, segtree, lazy, left, right) + query(rightChild, mid + 1, end, segtree, lazy, left, right);
  }

  // 단일 노드 값 갱신
  private static void update(int node, int start, int end, long[] segtree, int idx, long diff) {
    if (idx < start || end < idx)
      return;

    if (start == end) {
      segtree[node] += diff;
      return;
    }

    int leftChild = 2 * node;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    update(leftChild, start, mid, segtree, idx, diff);
    update(rightChild, mid + 1, end, segtree, idx, diff);
    segtree[node] = segtree[leftChild] + segtree[rightChild];
  }

  // 구간 합 조회 (서브트리 전체에서 발생한 부하 -> 상사 칭찬의 합)
  private static long query(int node, int start, int end, long[] segtree, int left, int right) {
    if (left > end || right < start)
      return 0;

    if (left <= start && end <= right)
      return segtree[node];

    int leftChild = 2 * node;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    return query(leftChild, start, mid, segtree, left, right) + query(rightChild, mid + 1, end, segtree, left, right);
  }
} //	Main-class-end
```