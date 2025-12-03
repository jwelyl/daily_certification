# 25_12_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11962 Counting Haybales

[11962번: Counting Haybales](http://boj.ma/11962/t)

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
    int n =  Integer.parseInt(st.nextToken());  //  건초 구역 수
    int q =  Integer.parseInt(st.nextToken());  //  쿼리 수

    long[] hayArr = new long[n + 1];          //  각 구역의 건초
    long[] sumSegtree = new long[4 * n + 1];  //  특정 구간의 건초 합 세그먼트 트리
    long[] minSegtree = new long[4 * n + 1];  //  특정 구간에서 건초가 가장 적은 구역의 건초 양 세그먼트 트리
    long[] lazy = new long[4 * n + 1];

    st = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++)
      hayArr[i] = Long.parseLong(st.nextToken());

    init(1, 1, n, hayArr, sumSegtree, minSegtree);

    for(int i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());
      char cmd = st.nextToken().charAt(0);
      int left = Integer.parseInt(st.nextToken());
      int right = Integer.parseInt(st.nextToken());
      int add = cmd == 'P' ? Integer.parseInt(st.nextToken()) : -1;

      if(add != - 1)  //  [left, right] 구간에 건초 add씩 추가
        addRange(1, 1, n, left, right, sumSegtree, minSegtree, lazy, add);
      else if(cmd == 'M') //  [left, right] 구간에서 최소 건초 더미 출력
        sb.append(getMin(1, 1, n, left, right, sumSegtree, minSegtree, lazy)).append("\n");
      else  //  [left, right] 구간의 건초 더미 합 출력
        sb.append(getSum(1, 1, n, left, right, sumSegtree, minSegtree, lazy)).append("\n");
    }

    System.out.print(sb);
  } //	main-end

  private static void init(int node, int start, int end, long[] hayArr, long[] sumSegtree, long[] minSegtree) {
    if(start == end) {
      sumSegtree[node] = hayArr[start];
      minSegtree[node] = hayArr[start];
      return;
    }

    int left = 2 * node;
    int right = 2 * node + 1;
    int mid = (start + end) / 2;

    init(left, start, mid, hayArr, sumSegtree, minSegtree);
    init(right, mid + 1, end, hayArr, sumSegtree, minSegtree);

    sumSegtree[node] = sumSegtree[left] + sumSegtree[right];
    minSegtree[node] = Math.min(minSegtree[left], minSegtree[right]);
  }

  private static long getSum(int node, int start, int end, int left, int right, long[] sumSegtree, long[] minSegtree, long[] lazy) {
    updateLazy(node, start, end, sumSegtree, minSegtree, lazy);

    if(left > end || right < start)
      return 0;

    if(left <= start && end <= right)
      return sumSegtree[node];

    int leftChild = 2 * node;
    int rightChild = 2 * node + 1;
    int mid = (start + end) / 2;

    return getSum(leftChild, start, mid, left, right, sumSegtree, minSegtree, lazy) + getSum(rightChild, mid + 1, end, left, right, sumSegtree, minSegtree, lazy);
  }

  private static long getMin(int node, int start, int end, int left, int right, long[] sumSegtree, long[] minSegtree, long[] lazy) {
    updateLazy(node, start, end, sumSegtree, minSegtree, lazy);

    if(left > end || right < start)
      return Long.MAX_VALUE;

    if(left <= start && end <= right)
      return minSegtree[node];

    int leftChild = 2 * node;
    int rightChild = 2 * node + 1;
    int mid = (start + end) / 2;

    return Math.min(getMin(leftChild, start, mid, left, right, sumSegtree, minSegtree, lazy), getMin(rightChild, mid + 1, end, left, right, sumSegtree, minSegtree, lazy));
  }

  //  [start, end] 구간을 담당하는 node에서 [left, right] 구간의 구간에 add만큼 추가
  private static void addRange(int node, int start, int end, int left, int right, long[] sumSegtree, long[] minSegtree, long[] lazy, long add) {
    updateLazy(node, start, end, sumSegtree, minSegtree, lazy);

    if(left > end || right < start) //  node가 담당하는 구간과 전혀 겹치지 않는 경우
      return;

    if(left <= start && end <= right) {
      lazy[node] = add;
      updateLazy(node, start, end, sumSegtree, minSegtree, lazy);
      return;
    }

    int leftChild = 2 * node;
    int rightChild = 2 * node + 1;
    int mid = (start + end) / 2;
    addRange(leftChild, start, mid, left, right, sumSegtree, minSegtree, lazy, add);
    addRange(rightChild, mid + 1, end, left, right, sumSegtree, minSegtree, lazy, add);

    sumSegtree[node] = sumSegtree[leftChild] + sumSegtree[rightChild];
    minSegtree[node] = Math.min(minSegtree[leftChild], minSegtree[rightChild]);
  }

  private static void updateLazy(int node, int start, int end, long[] sumSegtree, long[] minSegtree, long[] lazy) {
    if(lazy[node] != 0) {
      sumSegtree[node] += lazy[node] * (end - start + 1); //  누적합은 증가량 * ([start, end] 구간 길이)만큼 증가
      minSegtree[node] += lazy[node];                     //  최솟값은 증가량만큼 증가

      if(start!= end) { //  leaf-node가 아닐 경우
        lazy[2 * node] += lazy[node];
        lazy[2 * node + 1] += lazy[node];
      }

      lazy[node] = 0;
    }
  }
} //	Main-class-end
```