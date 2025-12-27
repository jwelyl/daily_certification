# 25_12_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13504 XOR 합

[13504번: XOR 합](http://boj.ma/13504/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_LEN = 32;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for (int tc = 1; tc <= t; tc++) {
      Trie trie = new Trie();
      int n = Integer.parseInt(br.readLine());

      int xor = 0;
      StringTokenizer st = new StringTokenizer(br.readLine());

      trie.insert(xorToBinary(xor));
      for(int i = 1; i <= n; i++) {
        xor ^= Integer.parseInt(st.nextToken());
        trie.insert(xorToBinary(xor));
      }

      sb.append(trie.getMaxXOR()).append("\n");
    }

    System.out.print(sb);
  }    //    main-end

  private static class TrieNode {
    private final TrieNode[] children = new TrieNode[2];

    public boolean isLeaf() {
      return children[0] == null && children[1] == null;
    }
  }

  private static class Trie {
    private final TrieNode root =  new TrieNode();

    //  첫번째 수부터 누적 xor한 값을 이진수로 변환하여 트라이에 삽입
    public void insert(String xorBinary) {
      TrieNode cur = this.root;

      for (int i = 0; i < xorBinary.length(); i++) {
        char bit = xorBinary.charAt(i);

        if(cur.children[bit - '0'] == null)
          cur.children[bit - '0'] = new TrieNode();

        cur = cur.children[bit - '0'];
      }
    }

    private int getMaxXOR() {
      int res = 0;

      for(int bit = 0; bit < 2; bit++) {
        if(this.root.children[bit] != null) {
          TrieNode cur = this.root.children[bit];
          TrieNode pair = this.root.children[1 - bit] == null ? cur : this.root.children[1 - bit];
          int xor = this.root.children[1 - bit] == null ? 0 : 1;

          res = Math.max(res, dfs(cur, pair, xor));
        }
      }

      return res;
    }
    private static int dfs(TrieNode cur, TrieNode pair, int xor) {
      if(cur.isLeaf() && pair.isLeaf())
        return xor;

      int res = 0;

      for(int bit = 0; bit < 2; bit++) {
        if(cur.children[bit] != null) {
          int pairBit = pair.children[1 - bit] == null ? bit : 1 - bit;
          int nxor = xor;
          nxor = (nxor << 1) + (bit == pairBit ? 0 : 1);

          res = Math.max(res, dfs(cur.children[bit], pair.children[pairBit], nxor));
        }
      }

      return res;
    }
  }

  private static String xorToBinary(int xor) {
    StringBuilder sb = new StringBuilder();
    String binary = Integer.toBinaryString(xor);

    for(int i = 0; i < MAX_LEN - binary.length(); i++)
      sb.append('0');

    return sb.append(binary).toString();
  }
}    //    Main-class-end
```