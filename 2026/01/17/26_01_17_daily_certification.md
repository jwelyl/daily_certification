# 26_01_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8044 Viruses

[8044번: Viruses](http://boj.ma/8044/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.HashMap;
import java.util.Map;
import java.util.Queue;

public class Main {
  private static final int FAIL = 2;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    Trie trie = new Trie();

    int n = Integer.parseInt(br.readLine());  //  바이러스 코드 개수

    for (int i = 0; i < n; i++)
      trie.insert(br.readLine());

    trie.failureFunction();
    trie.nullCheck();

    System.out.println(trie.hasSafeCycle() ? "TAK" : "NIE");
  } //  main-end

  private static class TrieNode {
    // children[0], children[1] : 입력 전이
    // children[2]             : 실패 링크
    public final TrieNode[] children = new TrieNode[3];
    public boolean isEnd = false; // true면 danger
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insert(String s) {
      TrieNode cur = root;

      for (int i = 0; i < s.length(); i++) {
        int bit = s.charAt(i) - '0';
        if (cur.children[bit] == null)
          cur.children[bit] = new TrieNode();

        cur = cur.children[bit];
      }

      cur.isEnd = true;
    }

    public void failureFunction() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      root.children[FAIL] = root;
      queue.offer(root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();

        for (int bit = 0; bit <= 1; bit++) {
          TrieNode next = cur.children[bit];
          if (next == null)
            continue;

          if (cur == root)
            next.children[FAIL] = root;
          else {
            TrieNode fail = cur.children[FAIL];

            while (fail != root && fail.children[bit] == null)
              fail = fail.children[FAIL];

            if (fail.children[bit] != null)
              fail = fail.children[bit];

            next.children[FAIL] = fail;
          }

          if (next.children[FAIL].isEnd)
            next.isEnd = true;

          queue.offer(next);
        }
      }
    }

    // null 전이 보완
    public void nullCheck() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      queue.offer(this.root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();
        for (int bit = 0; bit <= 1; bit++) {
          if (cur.children[bit] == null) {//  현재 노드에서 bit로 전이가 없을 경우
            if(cur == root) //  아무것도 읽지 않은 상태
              cur.children[bit] = root; //  그대로 루트
            else
              cur.children[bit] = cur.children[FAIL].children[bit]; //  실패 링크의 전이로 이동
          }
          else
            queue.offer(cur.children[bit]);
        }
      }
    }

    public boolean hasSafeCycle() {
      return dfs(root, new HashMap<>());
    }

    // 0 = 미방문, 1 = 방문중, 2 = 방문완료
    private boolean dfs(TrieNode node, Map<TrieNode, Integer> visitedMap) {
      Integer state = visitedMap.get(node);
      if (state != null) {
        if (state == 1)
          return true;   // cycle
        if (state == 2)
          return false;
      }

      visitedMap.put(node, 1);

      for (int bit = 0; bit <= 1; bit++) {
        TrieNode next = node.children[bit]; //  다음 노드 (상태 전이 정의해뒀으므로 null은 없음)
        if (next.isEnd) //  danger 상태는 갈 수 없음
          continue;
        if (dfs(next, visitedMap))  //  사이클 발견
          return true;
      }

      visitedMap.put(node, 2);  //  이 노드에서 갈 수 있는 곳은 다 갔음
      return false;
    }
  }
} //  Main-class-end

```

### BOJ 2250 트리의 높이와 너비

[2250번: 트리의 높이와 너비](http://boj.ma/2250/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.ArrayDeque;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  private static final int NONE = -1;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    BinTreeNode[] binTree = new BinTreeNode[n + 1];
    int root = 0;

    int[] answer;

    for (int i = 0; i <= n; i++)
      binTree[i] = new BinTreeNode();

    for (int i = 0; i < n; i++) {
      StringTokenizer tokens = new StringTokenizer(br.readLine());

      int num = Integer.parseInt(tokens.nextToken());
      int leftChild = Integer.parseInt(tokens.nextToken());
      int rightChild = Integer.parseInt(tokens.nextToken());

      binTree[num].leftChild = leftChild;
      binTree[num].rightChild = rightChild;

      if (leftChild != NONE)
        binTree[leftChild].parent = num;
      if (rightChild != NONE)
        binTree[rightChild].parent = num;
    }

    for (int i = 1; i <= n; i++) {
      if (binTree[i].parent == NONE) {
        root = i;
        break;
      }
    }

    dfs(root, 1, 1, binTree);
    answer = bfs(root, binTree);

    System.out.println(answer[0] + " " + answer[1]);
  } //  main-end

  private static int[] bfs(int root, BinTreeNode[] binTree) {
    Queue<Integer> queue = new ArrayDeque<>();
    queue.offer(root);

    int level = 1;
    int maxLevel = 0;
    int maxWidth = 0;

    while (!queue.isEmpty()) {
      int size = queue.size();
      int min = Integer.MAX_VALUE;
      int max = Integer.MIN_VALUE;

      for (int i = 0; i < size; i++) {
        int cur = queue.poll();

        int leftChild = binTree[cur].leftChild;
        int rightChild = binTree[cur].rightChild;

        if (leftChild != NONE)
          queue.offer(leftChild);
        if (rightChild != NONE)
          queue.offer(rightChild);

        min = Math.min(min, binTree[cur].pos);
        max = Math.max(max, binTree[cur].pos);
      }

      if (max - min + 1 > maxWidth) {
        maxWidth = max - min + 1;
        maxLevel = level;
      }

      level++;
    }

    return new int[] {maxLevel, maxWidth};
  }

  private static void dfs(int num, int pos, int level, BinTreeNode[] binTree) {
    binTree[num].level = level;

    if (binTree[num].leftChild == NONE && binTree[num].rightChild == NONE) {
      binTree[num].pos = pos;
      binTree[num].minPos = pos;
      binTree[num].maxPos = pos;
      return;
    }

    if (binTree[num].leftChild != NONE) {
      dfs(binTree[num].leftChild, pos, level + 1, binTree);
      binTree[num].pos = binTree[binTree[num].leftChild].maxPos + 1;
      binTree[num].minPos = binTree[binTree[num].leftChild].minPos;
    } else {
      binTree[num].pos = pos;
      binTree[num].minPos = pos;
    }

    if (binTree[num].rightChild != NONE) {
      dfs(binTree[num].rightChild, binTree[num].pos + 1, level + 1, binTree);
      binTree[num].maxPos = binTree[binTree[num].rightChild].maxPos;
    } else {
      binTree[num].maxPos = binTree[num].pos;
    }
  }

  private static class BinTreeNode {
    public int level = 0;
    public int pos = 0;
    public int leftChild = 0;
    public int rightChild = 0;
    public int parent = NONE;
    public int minPos = 0;
    public int maxPos = 0;
  }
} //  Main-class-end

```