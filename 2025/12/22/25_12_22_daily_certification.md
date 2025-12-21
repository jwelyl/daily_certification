# 25_12_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16903 수열과 쿼리 20

[16903번: 수열과 쿼리 20](http://boj.ma/16903/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_LEN = 30;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    Trie trie = new Trie();
    int m = Integer.parseInt(br.readLine());

    trie.insert(intToBinaryString(0));

    for(int q = 0; q < m; q++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int cmd = Integer.parseInt(st.nextToken());
      String binary = intToBinaryString(Integer.parseInt(st.nextToken()));

      switch (cmd) {
        case 1:
          trie.insert(binary);
          break;
        case 2:
          trie.delete(binary);
          break;
        case 3:
          sb.append(trie.xor(binary)).append("\n");
      }
    }

    System.out.print(sb);
  } //  main-end

  //  십진수 num을 30자리 이진수로 변경함
  private static String intToBinaryString(int num) {
    StringBuilder sb = new StringBuilder();
    String binary = Integer.toBinaryString(num);

    for(int i = 0; i < MAX_LEN - binary.length(); i++)
      sb.append('0');
    sb.append(binary);

    return sb.toString();
  }

  private static int binaryStringToInt(String binary) {
    return Integer.parseInt(binary, 2);
  }

  private static class TrieNode {
    public int bit; //  해당 노드에 저장된 비트 0/1
    public TrieNode parent;
    public final TrieNode[] children;
    public int cnt;         //  해당 노드를 지나가는 수의 개수
    public boolean end;

    public TrieNode(int bit, TrieNode parent) {
      this.bit = bit;
      this.parent = parent;
      this.children = new TrieNode[2];
      this.cnt = 1;
      this.end = false;
    }
  }

  private static class Trie {
    private final TrieNode root = new TrieNode(0, null);

    public void insert(String binary) {
      TrieNode cur = root;

      for(int i = 0; i < binary.length(); i++) {
        int bit = binary.charAt(i) - '0';

        if(cur.children[bit] == null) {
          cur.children[bit] = new TrieNode(bit, cur);
          cur.children[bit].parent = cur;
        }
        else
          cur.children[bit].cnt++;

        cur = cur.children[bit];

        if(i == binary.length() - 1)
          cur.end = true;
      }
    }

    public void delete(String binary) {
      TrieNode cur = root;

      for(int i = 0; i < binary.length(); i++) {
        int bit = binary.charAt(i) - '0';

        TrieNode child = cur.children[bit];
        child.cnt--;

        if(child.cnt == 0) {  //  삭제되서 더 이상 존재하지 않을 경우
          cur.children[bit].parent = null;
          cur.children[bit] = null;         //  부모 자식 사이의 연결 끊기
        }

        cur = child;
      }
    }

    public int xor(String binary) {
      StringBuilder sb = new StringBuilder();

      TrieNode cur = root;
      for(int i = 0; i < binary.length(); i++) {
        int bit = binary.charAt(i) - '0';

        if(cur.children[1 - bit] != null) {
          sb.append(1);
          cur = cur.children[1 - bit];
        }
        else {
          sb.append(0);
          cur = cur.children[bit];
        }
      }

      return binaryStringToInt(sb.toString());
    }
  }
} //  Main-class-end
```