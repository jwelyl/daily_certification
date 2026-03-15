# 26_03_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14570 나무 위의 구슬

[14570번: 나무 위의 구슬](http://boj.ma/14570/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int ROOT = 1;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());
    Node[] tree = new Node[n + 1];
    long k;

    for (int v = 0; v <= n; v++)
      tree[v] = new Node();

    for (int i = 1; i <= n; i++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      tree[i].left = Integer.parseInt(st.nextToken());
      tree[i].right = Integer.parseInt(st.nextToken());
    }

    k = Long.parseLong(br.readLine());

    dfs(ROOT, tree, k);
  } //  main-end

  private static void dfs(int cur, Node[] tree, long k) {
    int leftChild = tree[cur].left;
    int rightChild = tree[cur].right;

    if (leftChild == -1 && rightChild == -1) {
      System.out.println(cur);
      return;
    }

    if (leftChild != -1 && rightChild != -1) {
      if (k % 2 == 1L)
        dfs(leftChild, tree, k / 2 + 1);
      else
        dfs(rightChild, tree, k / 2);
    }
    else if (leftChild != -1)
      dfs(leftChild, tree, k);
    else
      dfs(rightChild, tree, k);
  }

  private static class Node {
    public int left = -1;
    public int right = -1;
  }
} //  Main-class-end
```