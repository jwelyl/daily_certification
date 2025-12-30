# 25_12_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13445 부분 수열 XOR

[13445번: 부분 수열 XOR](http://boj.ma/13445/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_LEN = 20;
  private static String K_BINARY;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    Trie trie = new Trie();
    int[] xorPrefixSum;
    long answer = 0;  //  K보다 작은 부분 수열 XOR 개수

    int n = Integer.parseInt(st.nextToken()); //  수열 길이
    K_BINARY = xorToBinary(Integer.parseInt(st.nextToken())); //  부분 수열 XOR 상한

    xorPrefixSum = new int[n + 1];

    trie.insert(xorToBinary(0));

    st = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++)
      xorPrefixSum[i] = xorPrefixSum[i - 1] ^ Integer.parseInt(st.nextToken());

    //  ai^ai+1^ai+^...^aj-1^aj = (a1^a2^a3+^...^aj-1^aj) ^ (a1^a2^a3+^...^ai-1)
    for(int i = 1; i <= n; i++) {
      String psBinary = xorToBinary(xorPrefixSum[i]); //   누적 xor 값
      answer += trie.getCount(psBinary);  //  이전에 trie에 저장된 누적 xor 값 중 psBinary와 xor해서 K 미만인 것의 개수
      trie.insert(psBinary);
    }

    System.out.println(answer);
  } //	main-end

  private static String xorToBinary(int xor) {
    String xorBinary = Integer.toBinaryString(xor);
    StringBuilder sb = new StringBuilder();

    for(int i = 0; i < MAX_LEN - xorBinary.length(); i++)
      sb.append('0');

    sb.append(xorBinary);

    return sb.toString();
  }

  private static class TrieNode {
    public TrieNode[] children;
    public int count;

    public TrieNode() {
      this.children = new TrieNode[2];
      this.count = 0;
    }
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    //  xor prefix sum을 트라이에 삽입
    public void insert(String xorBinary) {
      TrieNode cur = this.root;

      for(int i = 0; i < xorBinary.length(); i++) {
        int bit = xorBinary.charAt(i) - '0';

        if(cur.children[bit] == null) //  cur 노드의 자식 중 bit로 이어지는 자식이 없을 경우
          cur.children[bit] = new TrieNode();

        cur = cur.children[bit];
        cur.count++;
      }
    }

    public long getCount(String xorBinary) {
      return dfs(this.root, xorBinary, 0, true);
    }

    private long dfs(TrieNode cur, String numBinary, int idx, boolean tie) {
      if(cur == null)
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
} //	Main-class-end
```