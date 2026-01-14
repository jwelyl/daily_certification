# 26_01_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10256 돌연변이

[10256번: 돌연변이](http://boj.ma/10256/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int t = Integer.parseInt(br.readLine());  //  테스트 케이스 개수

    for (int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int n = Integer.parseInt(st.nextToken()); //  DNA 문자열 길이
      int m = Integer.parseInt(st.nextToken()); //  마커 길이

      Trie trie = new Trie();

      String dna = br.readLine();     //  dna
      String marker = br.readLine();  //  마커

      for (int from = 0; from < m; from++) {
        for (int to = from; to < m; to++) {
          String head = from == 0 ? "" : marker.substring(0, from);
          String body = marker.substring(from, to + 1);
          String tail = to == m - 1 ? "" : marker.substring(to + 1);

          body = new StringBuilder(body).reverse().toString();

          trie.insertWord(head + body + tail);
        }
      }

      trie.makeFailFunction();

      sb.append(trie.ahoCorasick(dna)).append("\n");
    }

    System.out.print(sb);
  }  //  main-end

  private static class TrieNode {
    private final TrieNode[] children = new TrieNode[4];
    private TrieNode fail;  //  실패 링크
    private boolean isEnd = false;
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insertWord(String word) {
      TrieNode cur = this.root;

      for (int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);
        int idx = ch == 'A' ? 0 : (ch == 'C' ? 1 : (ch == 'G' ? 2 : 3));

        if (cur.children[idx] == null)
          cur.children[idx] = new TrieNode();

        cur = cur.children[idx];
      }

      cur.isEnd = true;
    }

    public void makeFailFunction() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      this.root.fail = this.root; //  루트의 실패 링크는 자기 자신
      queue.offer(this.root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();

        for (int idx = 0; idx < 4; idx++) {
          TrieNode next = cur.children[idx];

          if(next == null)
            continue;

          if (cur == this.root)  //  루트의 바로 자식 노드의 실패 링크는 루트 노드로
            next.fail = this.root;
          else {  //  부모를 따라 가면서 실패 링크 찾기
            TrieNode failNode = cur.fail; //  부모의 실패 링크

            while (failNode != this.root && failNode.children[idx] == null)
              failNode = failNode.fail;

            if (failNode.children[idx] != null)
              failNode = failNode.children[idx];

            next.fail = failNode;
          }

          if (next.fail.isEnd)
            next.isEnd = true;

          queue.offer(next);
        }
      }
    }

    public int ahoCorasick(String text) {
      TrieNode cur = this.root;
      int cnt = 0;

      for (int i = 0; i < text.length(); i++) {
        char ch = text.charAt(i);
        int idx = ch == 'A' ? 0 : (ch == 'C' ? 1 : (ch == 'G' ? 2 : 3));

        while (cur != this.root && cur.children[idx] == null)
          cur = cur.fail;

        if (cur.children[idx] != null)
          cur = cur.children[idx];

        if (cur.isEnd)
          cnt++;
      }

      return cnt;
    }
  }
} //  Main-class-end
```