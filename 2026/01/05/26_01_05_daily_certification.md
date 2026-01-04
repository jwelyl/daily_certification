# 26_01_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5467 Type Printer

[5467번: Type Printer](http://boj.ma/5467/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.AbstractMap;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Deque;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int n = Integer.parseInt(br.readLine());
    Trie trie = new Trie();

    for (int i = 0; i < n; i++)
      trie.insert(br.readLine());

    trie.dfs();

    sb.append(trie.getCmdList().size()).append("\n");
    for (char cmd : trie.getCmdList())
      sb.append(cmd).append("\n");

    System.out.print(sb);
  } // main-end

  private static class TrieNode {
    private final Map<Character, TrieNode> children = new HashMap<>();
    private int remain; // 자식노드 줄기 중 가장 긴 줄기 길이
    private boolean end;
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();
    private final Deque<Character> cmdList = new ArrayDeque<>();

    public Deque<Character> getCmdList() {
      return cmdList;
    }

    public void insert(String word) {
      TrieNode cur = this.root;

      for (int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);

        if (!cur.children.containsKey(ch))
          cur.children.put(ch, new TrieNode());

        cur = cur.children.get(ch);
        cur.remain = Math.max(cur.remain, word.length() - i);

        if (i == word.length() - 1)
          cur.end = true;
      }
    }

    public void dfs() {
      this.dfs(this.root);

      while(!cmdList.isEmpty() && cmdList.peekLast() == '-')
        cmdList.pollLast();
    }

    private void dfs(TrieNode node) {
      if (node.end)
        cmdList.offerLast('P');

      List<Map.Entry<Integer, Character>> orderList = new ArrayList<>();

      for (Map.Entry<Character, TrieNode> entry : node.children.entrySet())
        orderList.add(new AbstractMap.SimpleEntry<>(entry.getValue().remain, entry.getKey()));

      Collections.sort(orderList, (o1, o2) -> Integer.compare(o1.getKey(), o2.getKey()));
      for (Map.Entry<Integer, Character> entry : orderList) {
        char ch = entry.getValue();
        cmdList.offerLast(ch);
        dfs(node.children.get(ch));
        cmdList.offerLast('-');
      }
    }
  }
} // Main-class-end
```

### BOJ 10654 Cow Jog

[10654번: Cow Jog](http://boj.ma/10654/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    int t = Integer.parseInt(st.nextToken());

    Deque<long[]> stack = new ArrayDeque<>();
    for(int i = 0; i < n; i++) {
      st = new StringTokenizer(br.readLine());
      int init = Integer.parseInt(st.nextToken());
      int velocity = Integer.parseInt(st.nextToken());
      long last = init + (long)velocity * t;

      while(!stack.isEmpty()) {
        long[] top = stack.peekLast();
        if(top[1] < last) //  이전 소에게 추월당하지 않는 경우
          break;

        stack.pollLast(); //  이전 소에게 추월당할 경우, 합쳐짐
      }

      stack.offerLast(new long[]{init, last});
    }

    System.out.println(stack.size());
  } // main-end
} // Main-class-end
```