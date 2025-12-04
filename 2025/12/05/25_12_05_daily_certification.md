# 25_12_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12895 화려한 마을

[12895번: 화려한 마을](http://boj.ma/12895/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuffer sb = new StringBuffer();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //  집의 개수
    int t = Integer.parseInt(st.nextToken()); //  칠할 색의 종류 (최대 30가지)
    int q = Integer.parseInt(st.nextToken()); //  쿼리 개수

    int[] colors = new int[n + 1];
    //  segtree[node] : node가 담당하는 범위 [start, end]에 해당하는 집들의 색의 비트 상태
    int[] segtree = new int[4 * n + 1];
    int[] lazy = new int[4 * n + 1];

    init(1, 1, n, segtree);

    for(int  i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());
      char cmd = st.nextToken().charAt(0);
      int left = Integer.parseInt(st.nextToken());
      int right = Integer.parseInt(st.nextToken());
      int color = cmd == 'C' ? Integer.parseInt(st.nextToken()) : -1;

      if(cmd == 'C')
        update(1, 1, n, Math.min(left, right), Math.max(left, right), segtree, lazy, color);
      else
        sb.append(Integer.bitCount(query(1, 1, n, Math.min(left, right), Math.max(left, right), segtree, lazy))).append("\n");
    }

    System.out.print(sb);
  } //	main-end

  private static void init(int node, int start, int end, int[] segtree) {
    if(start == end) {  //  leaf node일 경우
      segtree[node] = (1 << 1); //  최초에 모든 집은 색 1로 칠해진 상태
      return;
    }

    int leftChild = node * 2;
    int rightChild = leftChild + 1;
    init(leftChild, start, (start + end) / 2, segtree);
    init(rightChild, (start + end) / 2 + 1, end, segtree);

    segtree[node] = segtree[leftChild] | segtree[rightChild];
  }

  private static int query(int node, int start, int end, int left, int right, int[] segtree, int[] lazy) {
    lazyPropagate(node, start, end, segtree, lazy);

    if(left > end || right < start)
      return 0;

    if(left <= start && end <= right)
      return segtree[node];

    int leftChild = node * 2;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    return query(leftChild, start, mid, left, right, segtree, lazy) | query(rightChild, mid + 1, end, left, right, segtree, lazy);
  }

  private static void update(int node, int start, int end, int left, int right, int[] segtree, int[] lazy, int color) {
    lazyPropagate(node, start, end, segtree, lazy);

    if(left > end || right < start)
      return;

    if(left <= start && end <= right) {
      segtree[node] = (1 << color);
      lazy[node] = color;
      return;
    }

    int leftChild = node * 2;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    update(leftChild, start, mid, left, right, segtree, lazy, color);
    update(rightChild, mid + 1, end, left, right, segtree, lazy, color);

    segtree[node] = segtree[leftChild] | segtree[rightChild];
  }

  private static void lazyPropagate(int node, int start, int end, int[] segtree, int[] lazy) {
    if(lazy[node] != 0) {
      segtree[node] = (1 << lazy[node]);  //  해당 구간 전체가 lazy[node]의 색으로 덮임

      if(start != end) {
        lazy[node * 2] = lazy[node];
        lazy[node * 2 + 1] = lazy[node];
      }

      lazy[node] = 0;
    }
  }
} //	Main-class-end
```