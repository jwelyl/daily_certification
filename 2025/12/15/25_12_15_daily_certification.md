# 25_12_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2233 사과나무

[2233번: 사과나무](http://boj.ma/2233/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());
    char[] binary = br.readLine().toCharArray();
    StringTokenizer st = new StringTokenizer(br.readLine());
    int x = Integer.parseInt(st.nextToken()) - 1;
    int y = Integer.parseInt(st.nextToken()) - 1;
    int lca = 0;

    int[] parents = new int[n + 1];
    int[] depth = new int[n + 1];
    int[] startPosToNum = new int[2 * n];
    int[] endPosToNum = new int[2 * n];
    int[] numToStartPos = new int[n + 1];
    int[] numToEndPos = new int[n + 1];

    Deque<Integer> stack = new ArrayDeque<>();
    int num = 0;

    for (int i = 0; i < 2 * n; i++) {
      if (binary[i] == '0') {
        num++;

        if (!stack.isEmpty()) {
          parents[num] = stack.peekLast();
          depth[num] = depth[stack.peekLast()] + 1;
        }

        stack.offerLast(num);
        startPosToNum[i] = num;
        numToStartPos[num] = i + 1;
      } else {
        int child = stack.pollLast();
        endPosToNum[i] = child;
        numToEndPos[child] = i + 1;
      }
    }

    x = startPosToNum[x] == 0 ? endPosToNum[x] : startPosToNum[x];
    y = startPosToNum[y] == 0 ? endPosToNum[y] : startPosToNum[y];

    lca = lca(x, y, parents, depth);

    System.out.println(numToStartPos[lca] + " " + numToEndPos[lca]);
  } //	main-end

  private static int lca(int x, int y, int[] parents, int[] depth) {
    int xx = depth[x] > depth[y] ? x : y;
    int yy = depth[x] > depth[y] ? y : x;

    while (depth[xx] != depth[yy])
      xx = parents[xx];

    while (xx != yy) {
      xx = parents[xx];
      yy = parents[yy];
    }

    return xx;
  }
} //	Main-class-end
```