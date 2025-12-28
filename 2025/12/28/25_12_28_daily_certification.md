# 25_12_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24397 말해 xor NO!

[24397번: 말해 xor NO!](http://boj.ma/24397/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_LEN = 30;
  private static String K_BINARY;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    Trie trie = new Trie();

    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());
    long answer = 0;

    K_BINARY = numToBinary(Integer.parseInt(st.nextToken()));

    st = new StringTokenizer(br.readLine());
    for (int i = 0; i < n; i++)
      trie.insert(numToBinary(Integer.parseInt(st.nextToken())));

    st = new StringTokenizer(br.readLine());
    for (int i = 0; i < m; i++) {
      String b = numToBinary(Integer.parseInt(st.nextToken()));
      answer += trie.dfs(b);
    }

    System.out.println(answer);
  } //  main-end

  private static class TrieNode {
    TrieNode[] children = new TrieNode[2];
    int count = 0; // subtree size
  }

  private static class Trie {
    TrieNode root = new TrieNode();

    public void insert(String binary) {
      TrieNode cur = root;
      cur.count++;

      for (int i = 0; i < MAX_LEN; i++) {
        int bit = binary.charAt(i) - '0';
        if (cur.children[bit] == null)
          cur.children[bit] = new TrieNode();

        cur = cur.children[bit];
        cur.count++;
      }
    }

    public long dfs(String binary) {
      return dfs(root, binary, 0, true);
    }

    private long dfs(TrieNode cur, String numBinary, int idx, boolean tie) {
      if (cur == null)
        return 0;

      if (!tie) // 이미 xor < K 가 확정된 상태
        return cur.count;

      if (idx == MAX_LEN) //  xor == K인 상태, 조건 위반
        return 0;

      long res = 0;
      int numBit = numBinary.charAt(idx) - '0';
      int kBit = K_BINARY.charAt(idx) - '0';

      for (int bit = 0; bit < 2; bit++) {
        TrieNode next = cur.children[bit];
        if (next == null)
          continue;

        int xor = bit ^ numBit;

        if (xor < kBit)
          res += next.count;
        else if (xor == kBit)
          res += dfs(next, numBinary, idx + 1, true);
      }

      return res;
    }
  }

  private static String numToBinary(int num) {
    String binary = Integer.toBinaryString(num);
    StringBuilder sb = new StringBuilder();

    for (int i = 0; i < MAX_LEN - binary.length(); i++)
      sb.append('0');
    sb.append(binary);

    return sb.toString();
  }
} //  Main-class-ned

```