# 26_01_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9250 문자열 집합 판별

[9250번: 문자열 집합 판별](http://boj.ma/9250/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Queue;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    Trie trie = new Trie();

    //  단어 수
    int n = Integer.parseInt(br.readLine());
    int q;

    //  단어들을 트라이에 추가
    for (int i = 0; i < n; i++)
      trie.insertWord(br.readLine());

    trie.makeFailFunction();

    //  부분 문자열이 있는지 확인하려는 문자열 수
    q = Integer.parseInt(br.readLine());
    for (int i = 0; i < q; i++)
      sb.append(trie.ahoCorasick(br.readLine()) ? "YES\n" : "NO\n");

    System.out.print(sb);
  } //  main-end

  private static class TrieNode {
    private final TrieNode[] children = new TrieNode[26];
    private TrieNode fail;  //  실패 링크
    private boolean isEnd = false;
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insertWord(String word) {
      TrieNode cur = this.root;

      for (int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);

        if (cur.children[ch - 'a'] == null)
          cur.children[ch - 'a'] = new TrieNode();

        cur = cur.children[ch - 'a'];
      }

      cur.isEnd = true;
    }

    public void makeFailFunction() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      this.root.fail = this.root; //  루트의 실패 링크는 자기 자신
      queue.offer(this.root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();

        for (char ch = 'a'; ch <= 'z'; ch++) {
          TrieNode next = cur.children[ch - 'a'];

          if (next == null)
            continue;

          if (cur == this.root)  //  루트의 바로 자식 노드의 실패 링크는 루트 노드로
            next.fail = this.root;
          else {  //  부모를 따라 가면서 실패 링크 찾기
            TrieNode failNode = cur.fail; //  부모의 실패 링크

            while (failNode != this.root && failNode.children[ch - 'a'] == null)
              failNode = failNode.fail;
            if (failNode.children[ch - 'a'] != null)
              failNode = failNode.children[ch - 'a'];

            next.fail = failNode;
          }

          if (next.fail.isEnd)
            next.isEnd = true;

          queue.offer(next);
        }
      }
    }

    public boolean ahoCorasick(String word) {
      TrieNode cur = this.root;

      for (int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);

        while (cur != this.root && cur.children[ch - 'a'] == null)
          cur = cur.fail;

        if (cur.children[ch - 'a'] != null)
          cur = cur.children[ch - 'a'];

        if (cur.isEnd)
          return true;
      }

      return false;
    }
  }
} //  Main-class-end
```
