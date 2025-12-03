# 25_12_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18135 겨울나기

[18135번: 겨울나기](http://boj.ma/18135/t)

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
    int n =  Integer.parseInt(st.nextToken());  //  산책로 칸 수
    int m =  Integer.parseInt(st.nextToken());  //  영역 수

    int[] arr = new int[n + 1];     //  각 칸이 속한 영역
    long[] cnts = new long[m + 1];  //  각 영역에 존재하는 도토리 수
    long[] segtree = new long[4 * m + 1];
    long[] lazy = new long[4 * m + 1];

    for(int i = 1; i <= m; i++) {
      //  i번 영역
      st = new StringTokenizer(br.readLine());
      int from = Integer.parseInt(st.nextToken());
      int to = Integer.parseInt(st.nextToken());
      int cnt = Integer.parseInt(st.nextToken());

      for(int j = from; j <= to; j++)
        arr[j] = i; //  j번 칸은 i번 영역에 속함

      cnts[i] = cnt;  //  i번 영역에 도토리가 cnt개 존재
    }

    init(1, 1, m, cnts, segtree);
    while(true) {
      st = new StringTokenizer(br.readLine());
      int cmd = Integer.parseInt(st.nextToken());
      int x = Integer.parseInt(st.nextToken());
      int y = Integer.parseInt(st.nextToken());
      int z = -1;

      if(cmd == 0 && x == 0 && y == 0) {
        System.out.print(sb);
        break;
      }

      if(cmd == 1) {
        long res = 0;
        if(x <= y)
          res = query(1, 1, m, arr[x], arr[y], segtree, lazy);
        else {
          res = query(1, 1, m, arr[x], m, segtree, lazy);
          res += query(1, 1, m, 1, arr[y], segtree, lazy);
        }

        sb.append(res).append("\n");
      }
      else {
        z = Integer.parseInt(st.nextToken());

        if(x <= y)
          update(1, 1, m, arr[x], arr[y], z, segtree, lazy);
        else {
          update(1, 1, m, arr[x], m, z, segtree, lazy);
          update(1, 1, m, 1, arr[y], z, segtree, lazy);
        }
      }
    }
  } //	main-end

  private static void init(int node, int start, int end, long[] cnts, long[] segtree) {
    if(start == end) {
      segtree[node] = cnts[start];
      return;
    }

    int leftChild = node * 2;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    init(leftChild, start, mid, cnts, segtree);
    init(rightChild, mid + 1, end, cnts, segtree);

    segtree[node] = segtree[leftChild] + segtree[rightChild];
  }

  private static void lazyUpdate(int node, int start, int end, long[] segtree, long[] lazy) {
    if(lazy[node] != 0) {
      segtree[node] += lazy[node] * (end - start + 1);
      if(start != end) {
        int leftChild = node * 2;
        int rightChild = leftChild + 1;

        lazy[leftChild] += lazy[node];
        lazy[rightChild] += lazy[node];
      }

      lazy[node] = 0;
    }
  }

  private static void update(int node, int start, int end, int left, int right, long add, long[] segtree, long[] lazy) {
    lazyUpdate(node, start, end, segtree, lazy);
    if(start > right || end < left)
      return;

    if(left <= start && end <= right) {
      lazy[node] = add;
      lazyUpdate(node, start, end, segtree, lazy);
      return;
    }

    int leftChild = node * 2;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    update(leftChild, start, mid, left, right, add, segtree, lazy);
    update(rightChild, mid + 1, end, left, right, add, segtree, lazy);

    segtree[node] = segtree[leftChild] + segtree[rightChild];
  }

  private static long query(int node, int start, int end, int left, int right, long[] segtree, long[] lazy) {
    lazyUpdate(node, start, end, segtree, lazy);
    if(start > right || end < left)
      return 0;
    if(left <= start && end <= right)
      return segtree[node];

    int leftChild = node * 2;
    int rightChild = leftChild + 1;
    int mid = (start + end) / 2;

    return query(leftChild, start, mid, left, right, segtree, lazy) + query(rightChild, mid + 1, end, left, right, segtree, lazy);
  }
} //	Main-class-end
```