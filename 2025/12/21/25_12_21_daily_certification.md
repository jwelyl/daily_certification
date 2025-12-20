# 25_12_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9712 **Prefix Free Subsets**

[9712번: Prefix Free Subsets](http://boj.ma/9712/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for(int tc = 1; tc <= t; tc++) {
      Trie trie = new Trie();
      int n = Integer.parseInt(br.readLine());  //  단어 개수
      for(int i = 0; i < n; i++)
        trie.insert(br.readLine());

      sb.append("Case #").append(tc).append(": ").append(trie.getAnswer()).append("\n");
    }

    System.out.print(sb);
  } //  main-end

  private static class TrieNode {
    public final TrieNode[] nextArr;
    public boolean end;

    public TrieNode() {
      this.nextArr = new TrieNode[26];
      this.end = false;
    }
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insert(String word) {
      TrieNode cur = root;

      for(int i = 0; i < word.length(); i++) {
        char alphabet = word.charAt(i);

        if(cur.nextArr[alphabet - 'a'] == null)
          cur.nextArr[alphabet - 'a'] = new TrieNode();

        cur = cur.nextArr[alphabet - 'a'];
        if(i == word.length() - 1)
          cur.end = true;
      }
    }

    public long getAnswer() {
      return dfs(this.root);
    }

    private long dfs(TrieNode cur) {
      long res = 1;

      //  cur에서 멈추지 않고 다음 단어로 확장할 경우
      for(int i = 0; i < 26; i++) {
        if(cur.nextArr[i] != null)
          res *= dfs(cur.nextArr[i]);
      }

      if(cur.end) //  cur에서 멈출 경우
        res++;

      return res;
    }
  }
} //  Main-class-end
```