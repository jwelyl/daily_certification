# 26_01_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25948 Island Tour

[25948번: Islands Tour](http://boj.ma/25948/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX = 1_000_000;
  
  private static int n;
  private static int m;
  private static int group;
  private static int answer;
  private static final int[] next = new int[MAX];
  private static final int[] weightArr = new int[MAX];
  private static final int[] groupArr = new int[MAX];

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    m = Integer.parseInt(st.nextToken());
    n = Integer.parseInt(st.nextToken());

    for (int i = 0; i < n; i++)
      next[i] = -1;

    for (int i = 0; i < m; i++) {
      st = new StringTokenizer(br.readLine());
      int u = Integer.parseInt(st.nextToken());
      int v = Integer.parseInt(st.nextToken());
      next[u] = v;
    }

    for (int i = 0; i < n; i++) {
      if (groupArr[i] == 0) {
        group++;
        dfs(i);
      }
    }

    for (int i = 0; i < n; i++) {
      if (weightArr[i] == 0)
        fnd(i);
    }

    for (int i = 0; i < n; i++)
      answer = Math.max(answer, weightArr[i]);

    System.out.println(answer);
  } //  main-end

  private static void dfs(int node) {
    if (node == -1)
      return;

    if (groupArr[node] == 0) {
      groupArr[node] = group;
      dfs(next[node]);
    } else if (groupArr[node] == group) {
      int p = next[node];
      int sum = 1;

      while (p != node) {
        sum++;
        p = next[p];
      }

      p = next[node];
      weightArr[node] = sum;
      while (p != node) {
        weightArr[p] = sum;
        p = next[p];
      }
    }
  }

  private static int fnd(int node) {
    if (node == -1) {
      return 0;
    }
    if (weightArr[node] != 0) {
      return weightArr[node];
    }
    return weightArr[node] = fnd(next[node]) + 1;
  }
} //  Main-class-end
```