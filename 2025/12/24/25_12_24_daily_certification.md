# 25_12_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20919 XOR 자료구조

[20919번: XOR 자료구조](http://boj.ma/20919/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_LEN = 25;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for (int tc = 0; tc < t; tc++) {
      Trie trie = new Trie();
      StringTokenizer st = new StringTokenizer(br.readLine());
      int n = Integer.parseInt(st.nextToken());
      int q = Integer.parseInt(st.nextToken());

      st = new StringTokenizer(br.readLine());
      for (int i = 0; i < n; i++)
        trie.insert(intToBinaryString(Integer.parseInt(st.nextToken())));

      for(int i = 0; i < q; i++) {
        st = new StringTokenizer(br.readLine());
        int cmd = Integer.parseInt(st.nextToken());
        String binary = cmd <= 3 ? intToBinaryString(Integer.parseInt(st.nextToken())) : null;

        switch(cmd) {
          case 1:
            sb.append(trie.findMin(binary)).append("\n");
            break;
          case 2:
            sb.append(trie.findMax(binary)).append("\n");
            break;
          case 3:
            trie.insert(binary);
            sb.append(trie.size).append("\n");
            break;
          case 4:
            sb.append(Integer.parseInt(trie.removeMin(), 2)).append("\n");
            break;
          default:
            sb.append(Integer.parseInt(trie.removeMax(), 2)).append("\n");
        }
      }
    }

    System.out.print(sb);
  } //  main-end

  //  십진수 num을 25자리 이진수로 변경함
  private static String intToBinaryString(int num) {
    StringBuilder sb = new StringBuilder();
    String binary = Integer.toBinaryString(num);

    for (int i = 0; i < MAX_LEN - binary.length(); i++) {
      sb.append('0');
    }
    sb.append(binary);

    return sb.toString();
  }

  private static int binaryStringToInt(String binary) {
    return Integer.parseInt(binary, 2);
  }

  private static class TrieNode {
    public TrieNode parent;
    public final TrieNode[] children;

    public TrieNode(TrieNode parent) {
      this.parent = parent;
      this.children = new TrieNode[2];
      this.children[0] = null;
      this.children[1] = null;
    }

    public boolean isNotLeaf() {
      return children[0] != null || children[1] != null;
    }
  }

  private static class Trie {
    private final TrieNode root;
    private int size;

    public Trie() {
      this.root = new TrieNode(null);
      this.size = 0;
    }

    public void insert(String binary) {
      TrieNode cur = root;
      boolean inserted = false;

      for (int i = 0; i < binary.length(); i++) {
        int bit = binary.charAt(i) - '0';

        if (cur.children[bit] == null) {  //  이전에 없던 수가 들어오는 경우
          if(!inserted) { //  저장된 수의 개수는 한번만 증가
            inserted = true;
            this.size++;
          }

          cur.children[bit] = new TrieNode(cur);
          cur.children[bit].parent = cur;
        }

        cur = cur.children[bit];
      }
    }

    //  binary와 xor 연산을 취했을때 최소인 수 x를 찾아 (x xor binary) 값을 반환
    public int findMin(String binary) {
      StringBuilder sb = new StringBuilder();

      TrieNode cur = root;

      for(int i = 0; i < binary.length(); i++) {
        int bit = binary.charAt(i) - '0';

        if(cur.children[bit] != null) { //  같은 비트끼리 xor해야 값이 최소가 됨
          sb.append(0);
          cur = cur.children[bit];
        }
        else { // 같은 비트가 없다면 어쩔 수 없이 다른 비트로 이동
          sb.append(1);
          cur = cur.children[1 - bit];
        }
      }

      return binaryStringToInt(sb.toString());
    }

    //  binary와 xor 연산을 취했을때 최대인 수 x를 찾아 (x xor binary) 값을 반환
    public int findMax(String binary) {
      StringBuilder sb = new StringBuilder();

      TrieNode cur = root;

      for(int i = 0; i < binary.length(); i++) {
        int bit = binary.charAt(i) - '0';

        if(cur.children[1 - bit] != null) { //  다른 비트끼리 xor해야 값이 최대가 됨
          sb.append(1);
          cur = cur.children[1 - bit];
        }
        else { // 다른 비트가 없다면 어쩔 수 없이 같은 비트로 이동
          sb.append(0);
          cur = cur.children[bit];
        }
      }

      return binaryStringToInt(sb.toString());
    }

    public String removeMin() {
      StringBuilder sb = new StringBuilder();
      TrieNode leaf = root; //  제거될 수의 가장 마지막 노드

      while (leaf.isNotLeaf()) {  //  단말노드까지 이동
        if(leaf.children[0] != null) {
          sb.append(0);
          leaf = leaf.children[0];
        }
        else {
          sb.append(1);
          leaf = leaf.children[1];
        }
      }

      this.size--;
      remove(leaf);

      return sb.toString();
    }

    public String removeMax() {
      StringBuilder sb = new StringBuilder();
      TrieNode leaf = root; //  제거될 수의 가장 마지막 노드

      while (leaf.isNotLeaf()) {  //  단말노드까지 이동
        if(leaf.children[1] != null) {
          sb.append(1);
          leaf = leaf.children[1];
        }
        else {
          sb.append(0);
          leaf = leaf.children[0];
        }
      }

      this.size--;
      remove(leaf);

      return sb.toString();
    }

    //  leaf 노드인 cur부터 올라가면서 다른 수와 연결되어 있지않을 경우 제거함
    private void remove(TrieNode cur) {
      while(cur != root && !cur.isNotLeaf()) {
        if(cur.parent.children[0] == cur)
          cur.parent.children[0] = null;
        else
          cur.parent.children[1] = null;

        cur = cur.parent;
      }
    }
  }
} //  Main-class-end
```