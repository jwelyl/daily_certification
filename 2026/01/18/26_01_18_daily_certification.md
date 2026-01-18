# 26_01_18_daily_certification

```
[#018 koreii] 데일리인증 20260118
1. CS
- FastAPI API 구현
2. 알고리즘 (복습) 문제 풀이
- BOJ 17501 수식 트리 (Tree, DFS, Greedy, Sorting)
```

# Problem Solving (Algorithm & SQL)

### BOJ 17501 수식 트리

[17501번: 수식 트리](http://boj.ma/17501/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static int positive = 0;    // + 부호 피연산자 개수
  private static int negative = 0;    // - 부호 피연산자 개수

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());  // 트리 노드 개수
    int root = 2 * n - 1;
    Node[] tree = new Node[2 * n];  //  트리
    List<Integer> nums = new ArrayList<>();
    int answer = 0;

    for (int i = 0; i < 2 * n; i++)
      tree[i] = new Node(i);

    for (int i = 1; i <= n; i++) {
      int opr = Integer.parseInt(br.readLine());
      nums.add(opr);
    }

    for (int i = n + 1; i < 2 * n; i++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      char opt = st.nextToken().charAt(0);
      int left = Integer.parseInt(st.nextToken());
      int right = Integer.parseInt(st.nextToken());

      tree[i].opt = (opt == '-');
      tree[i].left = left;
      tree[i].right = right;
    }

    dfs(root, tree, false);

    // 절댓값 기준 내림차순 정렬
    nums.sort((a, b) -> Integer.compare(Math.abs(b), Math.abs(a)));

    int idx = 0;
    while (positive > 0 && negative > 0) {
      if (nums.get(idx) >= 0) {
        answer += nums.get(idx);
        positive--;
      } else {
        answer += -nums.get(idx);
        negative--;
      }
      idx++;
    }

    while (positive > 0) {
      answer += nums.get(idx++);
      positive--;
    }

    while (negative > 0) {
      answer += -nums.get(idx++);
      negative--;
    }

    System.out.println(answer);
  } //  main-end

  private static void dfs(int node, Node[] tree, boolean reversed) {
    int left = tree[node].left;
    int right = tree[node].right;

    if (left == -1 && right == -1) { // 리프 노드
      boolean nowSign = reversed != tree[node].opt;
      if (nowSign)
        negative++;
      else
        positive++;

      return;
    }

    dfs(left, tree, reversed);

    if (!tree[node].opt)   // '+'
      dfs(right, tree, reversed);
    else                // '-'
      dfs(right, tree, !reversed);
  }

  private static class Node {
    public int num;
    public boolean opt = false;
    public int left = -1;
    public int right = -1;

    public Node(int num) {
      this.num = num;
    }
  }
} //  Main-class-end

```