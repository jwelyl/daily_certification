# 25_12_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1998 이미지 압축

[1998번: 이미지 압축](http://boj.ma/1998/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;
import java.util.Map;
import java.util.HashMap;
import java.util.Set;
import java.util.HashSet;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    int[][] quadtree;
    int len = 1;

    Node root;

    int original = 0;     //  원본 쿼드트리 노드 개수
    int compressed = 0;   //  압축 쿼드트리 노드 개수

    while (len < Math.max(n, m))
      len *= 2;

    quadtree = new int[len][len];
    for (int y = 0; y < n; y++) {
      String input = br.readLine();
      for (int x = 0; x < m; x++)
        quadtree[y][x] = input.charAt(x) - '0';
    }

    root = recursion(0, 0, len - 1, len - 1, quadtree);

    // 원본 쿼드트리 정점 개수 출력
    original = countOriginal(root);
    // 압축
    root = compress(root);

    // 압축된 쿼드트리 정점 개수 출력
    compressed = countCompressed(root);

    System.out.print(original + " " + compressed);
  } //  main-end

  private static int countOriginal(Node root) {
    Queue<Node> queue = new ArrayDeque<>();
    int res = 0;

    queue.offer(root);
    res++;

    while (!queue.isEmpty()) {
      Node cur = queue.poll();

      if (cur.children != null) {
        for (Node child : cur.children) {
          queue.offer(child);
          res++;
        }
      }
    }

    return res;
  }

  private static int countCompressed(Node root) {
    Set<Node> visited = new HashSet<>();
    return dfs(root, visited);
  }

  private static int dfs(Node cur, Set<Node> visited) {
    if (visited.contains(cur))  //  이미 방문한 노드일 경우
      return 0;

    visited.add(cur);

    if (cur.children == null) //  leaf node일 경우
      return 1;

    int sum = 1;
    for (Node child : cur.children)
      sum += dfs(child, visited);

    return sum;
  }

  private static final Map<String, Node> map = new HashMap<>();

  private static Node compress(Node root) {
    // leaf node는 압축하지 않음
    if (root.children == null) {
      root.hash = "L(" + root.value + ")";
      return root;
    }

    // internal node
    Node compressed1 = compress(root.children[0]);
    Node compressed2 = compress(root.children[1]);
    Node compressed3 = compress(root.children[2]);
    Node compressed4 = compress(root.children[3]);

    root.hash = "I("
        + compressed1.hash + ","
        + compressed2.hash + ","
        + compressed3.hash + ","
        + compressed4.hash + ")";

    if (map.containsKey(root.hash))
      return map.get(root.hash);

    root.children = new Node[] {compressed1, compressed2, compressed3, compressed4};
    map.put(root.hash, root);

    return root;
  }

  private static Node recursion(int sy, int sx, int ey, int ex, int[][] quadtree) {
    Node res = new Node();

    if (sy == ey && sx == ex) {  // leaf
      res.same = 1;
      res.value = quadtree[sy][sx];
      res.children = null;
      return res;
    }

    int my = (sy + ey) / 2;
    int mx = (sx + ex) / 2;

    Node node1 = recursion(sy, sx, my, mx, quadtree);
    Node node2 = recursion(sy, mx + 1, my, ex, quadtree);
    Node node3 = recursion(my + 1, sx, ey, mx, quadtree);
    Node node4 = recursion(my + 1, mx + 1, ey, ex, quadtree);

    if (node1.same + node2.same + node3.same + node4.same == 4) {
      if (node1.value == node2.value && node1.value == node3.value && node1.value == node4.value) {
        res.same = 1;
        res.value = node1.value;
        res.children = null;
        return res;
      }
    }

    res.same = 0;
    res.children = new Node[] {node1, node2, node3, node4};
    return res;
  }

  private static class Node {
    public int same;
    public int value;
    public Node[] children;
    public String hash;
  }
} //  Main-class-end
```