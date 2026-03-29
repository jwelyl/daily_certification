# 26_03_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11840 XOR

[11840번: XOR](http://boj.ma/11840/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_LEN = 31; // 0 ~ 31 bit

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    int x = Integer.parseInt(st.nextToken());

    int[] sequence = new int[n + 1];
    Trie trie = new Trie();
    int prefix = 0;

    st = new StringTokenizer(br.readLine());
    for (int i = 1; i <= n; i++)
      sequence[i] = Integer.parseInt(st.nextToken());

    // p[0] = 0 삽입
    trie.insert(prefix, 0);

    int bestLen = 0;
    int bestStart = 1;

    for (int r = 1; r <= n; r++) {
      prefix ^= sequence[r];

      int lIndex = trie.query(prefix, x);

      if (lIndex != -1) {
        int len = r - lIndex;
        if (len > bestLen) {
          bestLen = len;
          bestStart = lIndex + 1;
        }
      }

      trie.insert(prefix, r);
    }

    System.out.println(bestStart + " " + bestLen);
  } //  main-end

  private static class TrieNode {
    public final TrieNode[] child = new TrieNode[2];
    public int minIndex = Integer.MAX_VALUE;
  }

  private static class Trie {
    public final TrieNode root = new TrieNode();

    // prefix XOR 삽입
    public void insert(int num, int index) {
      TrieNode cur = root;
      for (int i = MAX_LEN; i >= 0; i--) {
        int bit = (num >> i) & 1;

        if (cur.child[bit] == null)
          cur.child[bit] = new TrieNode();

        cur = cur.child[bit];
        cur.minIndex = Math.min(cur.minIndex, index);
      }
    }

    // p[r] ^ p[l] ≥ x 만족하는 최소 l 찾기
    public int query(int num, int x) {
      return dfs(root, num, x, MAX_LEN, true);
    }

    // tight: 지금까지 prefix가 x와 같은 상태인지
    private int dfs(TrieNode node, int num, int x, int bit, boolean tight) {
      if (node == null)
        return -1;
      if (bit < 0)
        return node.minIndex;

      int numBit = (num >> bit) & 1;
      int xBit = (x >> bit) & 1;

      int res = -1;

      if (tight) {
        if (xBit == 1) {
          int want = 1 - numBit;
          return dfs(node.child[want], num, x, bit - 1, true);
        } else {
          int want = 1 - numBit;
          if (node.child[want] != null)
            res = node.child[want].minIndex;

          int stay = numBit;
          int temp = dfs(node.child[stay], num, x, bit - 1, true);

          if (res == -1)
            return temp;

          if (temp == -1)
            return res;
          return Math.min(res, temp);
        }
      } else
        return node.minIndex;
    }
  }
} //  Main-class-end
```