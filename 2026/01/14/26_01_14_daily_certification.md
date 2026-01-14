# 26_01_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5735 Emoticons :-)

[5735번: Emoticons :-)](http://boj.ma/5735/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    while(true) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int n = Integer.parseInt(st.nextToken());    //    이모티콘 개수
      int m = Integer.parseInt(st.nextToken());    //    이모티콘 제거할 텍스트 수

      if(n == 0 && m == 0) {
        System.out.print(sb);
        break;
      }

      int answer = 0;    //    제거해야 할 이모티콘 개수

      Trie trie = new Trie();
      for(int i = 0; i < n; i++)
        trie.insert(br.readLine());

      trie.failureFunction();

      for(int i = 0; i < m; i++)
        answer += trie.ahoCorasick(br.readLine());

      sb.append(answer).append("\n");
    }
  }    //    main-end

  private static class TrieNode {
    public final Map<Character, TrieNode> children;
    public boolean isEnd;
    public TrieNode fail;

    public TrieNode() {
      this.children = new HashMap<>();
      this.isEnd = false;
      this.fail = null;
    }
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insert(String emoticon) {
      TrieNode cur = this.root;

      for(int i = 0; i < emoticon.length(); i++) {
        char ch = emoticon.charAt(i);

        if(!cur.children.containsKey(ch))
          cur.children.put(ch, new TrieNode());

        cur = cur.children.get(ch);
      }

      cur.isEnd = true;
    }

    public void failureFunction() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      this.root.fail = this.root;

      queue.offer(this.root);

      while(!queue.isEmpty()) {
        TrieNode cur = queue.poll();    //    현재 노드

        for(Map.Entry<Character, TrieNode> entry : cur.children.entrySet()) {
          char ch = entry.getKey();
          TrieNode next = entry.getValue();

          if(cur == this.root) //    루트 노드의 자식들의 실패 링크는 루트로 연결됨
            next.fail = this.root;
          else {
            TrieNode fail = cur.fail;    //    일단 부모 노드의 실패 링크

            //  더 거슬러 갈수 없거나, 접미사를 찾은 경우
            while (fail != this.root && !fail.children.containsKey(ch))
              fail = fail.fail;

            //  접미사를 찾은 경우
            if (fail.children.containsKey(ch))
              fail = fail.children.get(ch);

            next.fail = fail;
          }

          if(next.fail.isEnd)
            next.isEnd = true;

          queue.offer(next);
        }
      }
    }

    public int ahoCorasick(String text) {
      TrieNode cur = this.root;
      int cnt = 0;

      for(int i = 0; i < text.length(); i++) {
        char ch = text.charAt(i);

        while(cur != this.root && !cur.children.containsKey(ch))
          cur = cur.fail;

        if(cur.children.containsKey(ch))
          cur = cur.children.get(ch);

        if(cur.isEnd) {
          cnt++;
          cur = this.root;
        }
      }

      return cnt;
    }
  }
}    //    Main-class-end
```

### BOJ 10986 나머지 합

[10986번: 나머지 합](http://boj.ma/10986/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());

    Map<Integer, Long> map = new HashMap<>();
    
    int[] arr = new int[n + 1];
    long[] prefixSum = new long[n + 1];
    int[] sumMod = new int[n + 1];
    
    long answer = 0;

    st = new StringTokenizer(br.readLine());
    for (int i = 1; i <= n; i++) {
      arr[i] = Integer.parseInt(st.nextToken());
      prefixSum[i] = prefixSum[i - 1] + arr[i];
      sumMod[i] = (int) (prefixSum[i] % m);
    }

    for (int i = 0; i <= n; i++)
      map.put(sumMod[i], map.getOrDefault(sumMod[i], 0L) + 1);

    for(Map.Entry<Integer, Long> entry : map.entrySet()) {
      long num = entry.getValue();

      answer += nCombination2(num);
    }

    System.out.println(answer);
  }	//	main-end

  public static long nCombination2(long num) {
    return num * (num - 1) / 2;
  }
}	//	Main-class-end

```