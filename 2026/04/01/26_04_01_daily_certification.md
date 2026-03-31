# 26_04_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8916 이진 검색 트리

[8916번: 이진 검색 트리](http://boj.ma/8916/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  //  COMB[n][k] = nCk
  private static final int[][] COMB = new int[21][21];
  private static final int MOD = 9_999_991;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());
    init();

    for (int tc = 0; tc < t; tc++) {
      int n = Integer.parseInt(br.readLine());
      int[] permutation = new int[n];

      StringTokenizer st = new StringTokenizer(br.readLine());
      for(int i = 0; i < n; i++)
        permutation[i] = Integer.parseInt(st.nextToken());

      sb.append(dfs(permutation, 0, n - 1)).append("\n");
    }

    System.out.print(sb);
  } //  main-end

  private static int dfs(int[] arr, int left, int right) {
    if (right - left <= 0)
      return 1;

    int root = arr[left];

    int[] temp = new int[right - left];
    int idx = 0;

    // 왼쪽
    for (int i = left + 1; i <= right; i++) {
      if (arr[i] < root)
        temp[idx++] = arr[i];
    }

    int leftSize = idx;
    int mid = left + idx;

    // 오른쪽
    for (int i = left + 1; i <= right; i++) {
      if (arr[i] >= root)
        temp[idx++] = arr[i];
    }

    for (int i = 0; i < temp.length; i++)
      arr[left + 1 + i] = temp[i];

    int leftCnt = dfs(arr, left + 1, mid);
    int rightCnt = dfs(arr, mid + 1, right);

    int rightSize = (right - left) - leftSize;

    long res = (long) leftCnt * rightCnt % MOD;
    res = res * COMB[leftSize + rightSize][rightSize] % MOD;

    return (int) res;
  }

  private static void init() {
    for(int n = 0; n < COMB.length; n++) {
      COMB[n][0] = 1;
      COMB[n][n] = 1;

      for(int k =1; k < n; k++)
        COMB[n][k] = COMB[n - 1][k - 1] + COMB[n - 1][k];
    }
  }
} //  Main-class-end
```