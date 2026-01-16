# 26_01_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8044 Viruses

[8044번: Viruses](http://boj.ma/8044/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.HashMap;
import java.util.Map;
import java.util.Queue;

public class Main {
  private static final int FAIL = 2;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    Trie trie = new Trie();

    int n = Integer.parseInt(br.readLine());  //  바이러스 코드 개수

    for (int i = 0; i < n; i++)
      trie.insert(br.readLine());

    trie.failureFunction();
    trie.nullCheck();

    System.out.println(trie.hasSafeCycle() ? "TAK" : "NIE");
  } //  main-end

  private static class TrieNode {
    // children[0], children[1] : 입력 전이
    // children[2]             : 실패 링크
    public final TrieNode[] children = new TrieNode[3];
    public boolean isEnd = false; // true면 danger
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insert(String s) {
      TrieNode cur = root;

      for (int i = 0; i < s.length(); i++) {
        int bit = s.charAt(i) - '0';
        if (cur.children[bit] == null)
          cur.children[bit] = new TrieNode();

        cur = cur.children[bit];
      }

      cur.isEnd = true;
    }

    public void failureFunction() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      root.children[FAIL] = root;
      queue.offer(root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();

        for (int bit = 0; bit <= 1; bit++) {
          TrieNode next = cur.children[bit];
          if (next == null)
            continue;

          if (cur == root)
            next.children[FAIL] = root;
          else {
            TrieNode fail = cur.children[FAIL];

            while (fail != root && fail.children[bit] == null)
              fail = fail.children[FAIL];

            if (fail.children[bit] != null)
              fail = fail.children[bit];

            next.children[FAIL] = fail;
          }

          if (next.children[FAIL].isEnd)
            next.isEnd = true;

          queue.offer(next);
        }
      }
    }

    // null 전이 보완
    public void nullCheck() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      queue.offer(this.root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();
        for (int bit = 0; bit <= 1; bit++) {
          if (cur.children[bit] == null) {//  현재 노드에서 bit로 전이가 없을 경우
            if(cur == root) //  아무것도 읽지 않은 상태
              cur.children[bit] = root; //  그대로 루트
            else
              cur.children[bit] = cur.children[FAIL].children[bit]; //  실패 링크의 전이로 이동
          }
          else
            queue.offer(cur.children[bit]);
        }
      }
    }

    public boolean hasSafeCycle() {
      return dfs(root, new HashMap<>());
    }

    // 0 = 미방문, 1 = 방문중, 2 = 방문완료
    private boolean dfs(TrieNode node, Map<TrieNode, Integer> visitedMap) {
      Integer state = visitedMap.get(node);
      if (state != null) {
        if (state == 1)
          return true;   // cycle
        if (state == 2)
          return false;
      }

      visitedMap.put(node, 1);

      for (int bit = 0; bit <= 1; bit++) {
        TrieNode next = node.children[bit]; //  다음 노드 (상태 전이 정의해뒀으므로 null은 없음)
        if (next.isEnd) //  danger 상태는 갈 수 없음
          continue;
        if (dfs(next, visitedMap))  //  사이클 발견
          return true;
      }

      visitedMap.put(node, 2);  //  이 노드에서 갈 수 있는 곳은 다 갔음
      return false;
    }
  }
} //  Main-class-end

```

### BOJ 20160 **야쿠르트 아줌마 야쿠르트 주세요**

[20160번: 야쿠르트 아줌마 야쿠르트 주세요](http://boj.ma/20160/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static final int NONE = Integer.MAX_VALUE;
  private static final long INF = 100_000L * 100_000L * 10 + 1;

  private static final int[] YOGURT = new int[10];

  private static int answer = NONE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    //	정점 개수
    int n = Integer.parseInt(st.nextToken());
    //	간선 개수
    int m = Integer.parseInt(st.nextToken());

    List<Node>[] graph = new ArrayList[n + 1];
    long[] yogurtDist = new long[n + 1];
    long[] myDist = new long[n + 1];
    for(int v = 0; v <= n; v++)
      graph[v] = new ArrayList<>();

    for(int e = 0; e < m; e++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int c = Integer.parseInt(st.nextToken());

      graph[v1].add(new Node(v2, c));
      graph[v2].add(new Node(v1, c));
    }

    st = new StringTokenizer(br.readLine());
    for(int v = 0; v < 10; v++)
      YOGURT[v] = Integer.parseInt(st.nextToken());

    int myStart = Integer.parseInt(br.readLine());
    dijkstra(myStart, 0, graph, myDist);

    int cur = YOGURT[0];	//	현재 요구르트 위치
    long time = 0;			//	그때의 시간

    if(myDist[cur] <= time) {
      if(cur < answer)
        answer = cur;
    }

    int idx = 1;
    while(idx < 10) {
      int next = YOGURT[idx];	//	다음 요구르트 위치

      dijkstra(cur, time, graph, yogurtDist);

      if(yogurtDist[next] == INF) {	//	다음 요구르트 위치로 갈 수 없으면
        idx++;
        continue;
      }

      time = yogurtDist[next];
      cur = next;
      idx++;

      if(myDist[cur] <= time) {
        if(cur < answer)
          answer = cur;
      }
    }

    System.out.println(answer == NONE ? -1 : answer);
  }	//	main-end

  private static class Node implements Comparable<Node> {
    int vertex;
    long cost;

    public Node(int vertex, long cost) {
      this.vertex = vertex;
      this.cost = cost;
    }

    public int compareTo(Node other) {
      return Long.compare(this.cost, other.cost);
    }
  }

  private static void dijkstra(int start, long startTime, List<Node>[] graph, long[] dist) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    Arrays.fill(dist, INF);
    dist[start] = startTime;
    pq.offer(new Node(start, dist[start]));

    while(!pq.isEmpty()) {
      Node cur = pq.poll();
      int cv = cur.vertex;
      long cc = cur.cost;

      if(dist[cv] < cc)
        continue;

      for(Node next : graph[cv]) {
        int nv = next.vertex;
        long nc = cc + next.cost;

        if(nc < dist[nv]) {
          dist[nv] = nc;
          pq.offer(new Node(nv, dist[nv]));
        }
      }
    }
  }
}	//	Main-class-end
```