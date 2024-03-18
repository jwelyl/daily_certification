# 24_03_19_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 5670 휴대폰 자판**

[5670번: 휴대폰 자판](https://www.acmicpc.net/problem/5670)

트라이라는 것을 모르면 문제만 읽어서는 이해가 어려울 수 있다. 트라이 문제를 골라서 푼 것이기 때문에 트라이를 그림을 그려서 풀어보니 어렵지 않았다.

**예제 1**

![ex1.jpeg](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ex1.jpeg)

hell/hello와 heaven처럼 he 이후로 분기가 갈라질 경우 추가적으로 한 번 더 입력해야 한다. goodbye처럼 분기가 갈라지지 않을 경우 최초 g만 입력하면 한 번에 goodbye를 찾을 수 있다.

hell/hello의 경우 hell까지는 2번의 입력으로 찾을 수 있지만 hello를 찾기 위해서는 o를 추가로 입력해야 하므로 총 3번의 입력이 필요하다. 어떤 단어를 찾아 중간 노드에 도착했는데, 해당 노드에서 끝나는 단어가 존재할 경우 찾으려는 단어의 다음 노드로 가기 위해서는 한 번의 입력이 더 필요하다.

**예제 2**

![ex2.jpeg](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ex2.jpeg)

모든 단어가 h로 시작할 경우 h는 입력할 필요 없는거 아닌가 생각할 수 있지만 예제 2에 의하면 h는 한번은 입력을 무조건 해야 한다.

**예제 3**

![ex3.jpeg](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ex3.jpeg)

아래 코드는 예제 3을 기준으로 구현한 것이다. 예제 1,2에서 언급한 경우를 전부 고려하여 예제 3을 맞출 수 있게 구현하면 별 무리 없이 정답을 받을 수 있다.

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private final static StringBuilder sb = new StringBuilder();

  private static int n;    //    단어 개수
  private final static List<String> wordList = new ArrayList<String>();    //    단어 리스트
  private static Trie trie;
  private static double cnt = 0;

  public static void main(String[] args) throws IOException {
    while(true) {
      String input = br.readLine();

      if(input == null) {
        System.out.print(sb);
        break;
      }

      n = Integer.parseInt(input);
      trie = new Trie();
      wordList.clear();
      cnt = 0;

      for(int i = 0; i < n; i++) {
        String word = br.readLine();

        wordList.add(word);   //    단어 리스트에 단어 삽입
        trie.insert(word);    //    트라이에 단어 삽입
      }

      for(String word : wordList)    //    단어 리스트에 존재하는 단어 word
        cnt += trie.find(word);    //    word를 검색하기 위해 클릭해야 하는 버튼 수

      sb.append(String.format("%.2f\n", cnt / n));
    }
  }    //   main-end

  private static class TrieNode {
    public Map<Character, TrieNode> children = new HashMap<>();
    public boolean isLast = false;
  }

  private static class Trie {
    private TrieNode root = new TrieNode();

    //    트라이에 단어 word 삽입
    public void insert(String word) {
      TrieNode curNode = root;    //    현재 노드

      for(int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);    //    단어의 현재 문자
        if(!curNode.children.containsKey(ch))    //    현재 노드의 자식 노드 중 현재 문자에 해당하는 자식 노드가 없을 경우
          curNode.children.put(ch, new TrieNode());    //    현재 문자에 해당하는 자식 노드 생성 후 삽입

        curNode = curNode.children.get(ch);    //    현재 문자에 해당하는 자식 노드를 현재 노드로 함
      }

      curNode.isLast = true;    //    마지막 문자에 해당하는 노드를 마지막 노드로 함
    }

    //    트라이에서 word를 찾고 입력 횟수를 반환함
    public int find(String word) {
      int cnt = 1;    //    최소 한 번은 버튼 클릭해야 함
      TrieNode curNode = root.children.get(word.charAt(0));    //    찾으려는 단어의 첫 번째 문자에 해당하는 노드로 이동

      for(int i = 1; i < word.length(); i++) {
        char nextCh = word.charAt(i);    //    현재 노드에서 찾아야 하는 다음 문자
        TrieNode nextNode = curNode.children.get(nextCh);    //    다음 문자에 해당하는 다음 노드

        if(curNode.children.size() > 1)    //    현재 노드에서 다음 노드까지 한 분기가 아닐 경우
          cnt++;    //    다음 노드로 가기 위해선 한 번 더 클릭해야 함
        else {
          if(i - 1 != word.length() && curNode.isLast)    //    아직 더 가야 하는데 현재 노드는 끝 노드일 경우
            cnt++;    //    다음 노드로 가기 위해선 한 번 더 클릭해야 함
        }

        curNode = nextNode;    //    다음 노드로 이동
      }

      return cnt;
    }
  }
}   //   Main-class-end
```

애초에 트라이 문제를 골라서 푼 것이기 때문에 트라이로 접근했지만, 백준이 아니라 실제 시험에서 이 문제를 만났다면 트라이를 떠올릴 수 있을지 모르겠다. 실제 시험에서도 트라이를 떠올리려면 그만큼 트라이 관련 문제를 많이 풀어봐야겠지.