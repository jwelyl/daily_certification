# 25_12_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16906 욱제어

[16906번: 욱제어](http://boj.ma/16906/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    Trie trie = new Trie();

    int n = Integer.parseInt(br.readLine());
    List<Word> words = new ArrayList<>();
    StringTokenizer st = new StringTokenizer(br.readLine());
    for (int nth = 0; nth < n; nth++)
      words.add(new Word(nth, Integer.parseInt(st.nextToken())));

    Collections.sort(words);  //  길이 순으로 정렬
    for (Word word : words) {
      if (!trie.insert(word)) {
        System.out.println(-1);
        return;
      }
      word.length = 1_000;
    }

    //  모두 삽입 후 단어 입력 순서대로 재정렬
    Collections.sort(words);
    sb.append("1\n");
    for (Word word : words) {
      for (char ch : word.word)
        sb.append(ch);
      sb.append("\n");
    }

    System.out.print(sb);
  } //  main-end

  private static class Word implements Comparable<Word> {
    public final int nth;       //  단어 입력 순서
    public int length;          //  단어 길이
    public char[] word;

    public Word(int nth, int length) {
      this.nth = nth;
      this.length = length;
      this.word = new char[length];
    }

    @Override
    public int compareTo(Word other) {
      //  길이가 짧은 것부터 trie에 삽입 시도
      int res = Integer.compare(this.length, other.length);

      if(res == 0)
        res = Integer.compare(this.nth, other.nth);

      return res;
    }
  }

  private static class TrieNode {
    public final TrieNode[] children;
    public boolean end;

    public TrieNode() {
      this.children = new TrieNode[2];
      this.end = false;
    }
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    //  단어 word를 추가할 수 있으면 true, 없으면 false
    public boolean insert(Word word) {
      return insert(this.root.children[0], this.root, word, 0, 0) || insert(this.root.children[1], this.root, word, 1, 0);
    }

    private boolean insert(TrieNode cur, TrieNode parent, Word word, int bit, int idx) {
      if (cur == null) {
        parent.children[bit] = new TrieNode();
        cur = parent.children[bit];
      }

      if (cur.end)
        return false;

      word.word[idx] = (char) (bit + '0');
      if (idx == word.length - 1) {
        cur.end = true;
        return true;
      }

      return insert(cur.children[0], cur, word, 0, idx + 1) || insert(cur.children[1], cur, word, 1, idx + 1);
    }
  }
} //  Main-class-end
```