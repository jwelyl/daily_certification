# 25_11_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32322 Hotel Rooms

[32322번: Hotel Rooms](http://boj.ma/32322/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //  방 개수
    int q = Integer.parseInt(st.nextToken()); //  쿼리 개수

    //  segtree[node] : node가 담당하는 범위의 누적합 구하기
    //  segtree[1] : 1 ~ n의 누적합
    int[] segtree = new int[4 * n + 1];
    init(segtree, 1,1, n);

    for(int i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());
      char cmd = st.nextToken().charAt(0);

      if(cmd == 'A') {
        int left = Integer.parseInt(st.nextToken());
        int right = Integer.parseInt(st.nextToken());
        sb.append(query(segtree, 1, 1, n, left, right)).append("\n");
      }
      else {
        int nth = Integer.parseInt(st.nextToken());
        update(segtree, 1, nth, 1, n);
      }
    }

    System.out.print(sb);
  }    //    main-end

  private static void init(int[] segtree, int node, int start, int end) {
    if(start == end) {  //  리프노드, 즉, [start, start]를 담당하는 노드일 경우
      segtree[node] = 1;
      return;
    }

    int leftChild = node * 2;
    int rightChild = node * 2 + 1;
    int mid = (start + end) / 2;

    init(segtree, leftChild, start, mid);
    init(segtree, rightChild, mid + 1, end);

    segtree[node] = segtree[leftChild] + segtree[rightChild];
  }

  //  [left, right]에 해당하는 범위의 누적합을 반환함
  private static int query(int[] segtree, int node, int start, int end, int left, int right) {
    if(left > end || right < start) //  [left, right]가 node가 담당하는 범위를 벗어날 경우
      return 0;

    if(left <= start && end <= right)
      return segtree[node];

    int leftChild = node * 2;
    int rightChild = node * 2 + 1;
    int mid = (start + end) / 2;
    int leftSum = query(segtree, leftChild, start, mid, left, right);
    int rightSum = query(segtree, rightChild, mid + 1, end, left, right);

    return leftSum + rightSum;
  }

  //  nth(1 <= nth <= n)번째 값을 1에서 0으로 변경
  //  누적합 변경
  private static void update(int[] segtree, int node, int nth, int start, int end) {
    if(start == end) {  //  리프노드, 즉, [start, start]를 담당하는 노드일 경우
      segtree[node] = 0;
      return;
    }

    int leftChild = node * 2;
    int rightChild = node * 2 + 1;
    int mid = (start + end) / 2;

    if(nth <= mid)
      update(segtree, leftChild, nth, start, mid);
    else
      update(segtree, rightChild, nth, mid + 1, end);

    segtree[node] = segtree[leftChild] + segtree[rightChild];
  }
}    //    Main-class-end
```