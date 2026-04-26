# 26_04_27_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 18251 내 생각에 A번인 단순 dfs 문제가 이 대회에서 E번이 되어버린 건에 관하여 (Easy)**

[](http://boj.ma/18251/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());

    int h = (int) (Math.log(n + 1) / Math.log(2));
    long[][] arr2D = new long[h][n];
    long answer = 0;

    Node[] tree = new Node[n + 1];
    for (int i = 0; i <= n; i++)
      tree[i] = new Node();

    StringTokenizer st = new StringTokenizer(br.readLine());

    boolean allNeg = true;          // 모든 노드의 가중치가 음수일 경우 true
    long maxValue = Long.MIN_VALUE; // 가중치 중 최댓값

    tree[1].weight = Integer.parseInt(st.nextToken());
    if (tree[1].weight > 0) {
      allNeg = false;
    }
    maxValue = Math.max(maxValue, tree[1].weight);

    for (int i = 2; i <= n; i++) {
      int parentIdx = i / 2;
      Node parent = tree[parentIdx];

      tree[i].weight = Integer.parseInt(st.nextToken());
      if (tree[i].weight > 0) {
        allNeg = false;
      }
      maxValue = Math.max(maxValue, tree[i].weight);

      if (i % 2 == 0) {
        parent.left = i;
      } else {
        parent.right = i;
      }
    }

    // 모든 노드 가중치가 음수일 경우: 그나마 큰 하나만 포함
    if (allNeg) {
      System.out.println(maxValue);
      return;
    }

    // 좌표 계산 및 arr2D 채우기
    dfs(1, 0, 0, tree, arr2D);

    // 최대 부분 직사각형 합
    answer = kadane(arr2D, h, n);
    System.out.println(answer);
  } //  main-end

  private static long kadane(long[][] arr2D, int h, int n) {
    long res = Long.MIN_VALUE;

    for (int i = 0; i < h; i++) {
      long[] sum = new long[n];

      for (int j = i; j < h; j++) {
        for (int k = 0; k < n; k++)
          sum[k] += arr2D[j][k];

        long curMax = sum[0];
        long max = sum[0];

        for (int k = 1; k < n; k++) {
          curMax = Math.max(curMax + sum[k], sum[k]);
          max = Math.max(max, curMax);
        }

        res = Math.max(res, max);
      }
    }

    return res;
  }

  private static void dfs(int v, int yPos, int xPos, Node[] tree, long[][] arr2D) {
    Node node = tree[v];
    int left = node.left;
    int right = node.right;
    node.y = yPos;

    // 단말 노드
    if (left == -1 && right == -1) {
      node.x = xPos;
      node.biggestX = xPos;
      arr2D[node.y][node.x] = node.weight;
      return;
    }

    // 왼쪽 서브트리
    dfs(left, yPos + 1, xPos, tree, arr2D);

    // 현재 노드의 x 좌표는 왼쪽 서브트리의 가장 오른쪽 좌표 + 1
    node.x = tree[left].biggestX + 1;
    arr2D[node.y][node.x] = node.weight;

    // 오른쪽 서브트리: 가장 왼쪽 x 좌표는 현재 노드 x + 1
    dfs(right, yPos + 1, node.x + 1, tree, arr2D);

    // 이 서브트리에서 가장 오른쪽 x 좌표는 오른쪽 자식 서브트리의 biggestX
    node.biggestX = tree[right].biggestX;
  }

  private static class Node {
    public int y;
    public int x;
    public int weight;
    public int biggestX;  // 이 노드를 루트로 하는 서브트리에서 가장 오른쪽 노드의 x 좌표
    public int left = -1;
    public int right = -1;
  }
} //  Main-class-end
```