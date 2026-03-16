# 26_03_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22863 원상 복구 (large)

[22863번: 원상 복구 (large)](http://boj.ma/22863/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX = (int)Math.ceil(Math.log(Math.pow(10, 15)) / Math.log(2));

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    long k = Long.parseLong(st.nextToken());

    int[] p = new int[n + 1];
    int[][] sparseTable = new int[MAX + 1][n + 1];
    int[] answer = new int[n + 1];

    st = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++)
      p[i] = Integer.parseInt(st.nextToken());
    st = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++)
      sparseTable[0][i] = Integer.parseInt(st.nextToken());

    makeSparseTable(n, sparseTable);
    getAnswer(n, k, p, sparseTable, answer);

    for(int i = 1; i <= n; i++)
      sb.append(answer[i]).append(" ");
    System.out.println(sb);
  }    //    main-end

  private static void getAnswer(int n, long k, int[] p, int[][] sparseTable, int[] answer) {
    for(int i = 1; i <= n; i++) {
      int idx = i;

      for(int d = MAX; d >= 0; d--) {
        if((k & (1L << d)) != 0)
          idx = sparseTable[d][idx];
      }

      answer[idx] = p[i];
    }
  }

  private static void makeSparseTable(int n, int[][] sparseTable) {
    for (int d = 1; d <= MAX; d++) {
      for (int i = 1; i <= n; i++)
        sparseTable[d][i] = sparseTable[d - 1][sparseTable[d - 1][i]];
    }
  }
}    //    Main-class-end
```