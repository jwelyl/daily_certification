# 25_12_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5446 용량 부족

[5446번: 용량 부족](http://boj.ma/5446/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Queue;

public class Main {
  private static final int MAX = 63;
  private static final int DOT = '.';
  private static final int DIGIT = '0' - 1;
  private static final int CAPITAL = 'A' - 11;
  private static final int SMALL = 'a' - 37;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for (int tc = 1; tc <= t; tc++) {
      Trie trie = new Trie();
      int n1 = Integer.parseInt(br.readLine()); //  지워야 할 파일 개수
      int n2 = 0;                               //  지우면 안되는 파일 개수

      for (int i = 0; i < n1; i++)
        trie.insert(br.readLine(), false);

      n2 = Integer.parseInt(br.readLine());

      for (int i = 0; i < n2; i++)
        trie.insert(br.readLine(), true);

      sb.append(trie.minRmCount()).append("\n");
    }

    System.out.print(sb);
  }    //    main-end

  private static class TrieNode {
    private final TrieNode[] children;
    private boolean end;
    private boolean undeletable;

    public TrieNode() {
      this.children = new TrieNode[MAX];
      this.end = false;
      this.undeletable = false;
    }
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insert(String word, boolean undeletable) {
      TrieNode cur = this.root;
      if(undeletable)
        cur.undeletable = true;

      for (int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);
        int idx = getIdx(ch);

        if (cur.children[idx] == null)
          cur.children[idx] = new TrieNode();

        if (undeletable)
          cur.children[idx].undeletable = true;

        if (i == word.length() - 1 && !undeletable)
          cur.children[idx].end = true;

        cur = cur.children[idx];
      }
    }

    public int minRmCount() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      int res = 0;

      queue.offer(this.root);

      while(!queue.isEmpty()) {
        TrieNode cur = queue.poll();

        if (!cur.undeletable) {
          res++;
          continue;
        }

        if(cur.end)
          res++;

        for(int i = 0; i < MAX; i++) {
          if (cur.children[i] == null)
            continue;

          queue.offer(cur.children[i]);
        }
      }

      return res;
    }

    private static int getIdx(char ch) {
      return ch == '.' ? (ch - DOT) : (('0' <= ch && ch <= '9') ? (ch - DIGIT) : (('A' <= ch && ch <= 'Z') ? (ch - CAPITAL) : (ch - SMALL)));
    }
  }
}    //    Main-class-end
```