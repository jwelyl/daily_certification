# 24_05_26_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 새치기 2**

[https://www.codetree.ai/missions/8/problems/cut-in-line2/description](https://www.codetree.ai/missions/8/problems/cut-in-line2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/cut-in-line2/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final String HEAD = "HEAD@@";
  private static final String TAIL = "TAIL##";

  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;

  //  Key : 노드 이름, Value : 노드
  private static final Map<String, Node> nodeMap = new HashMap<>();

  private static Node[] heads;    //  heads[i] : i번째 연결리스트 head
  private static Node[] tails;    //  tails[i] : i번째 연결리스트 tail

  private static int n;   //  사람 수
  private static int m;   //  줄 수
  private static int q;   //  문자 수
  private static int x;   //  n / m

  public static void main(String[] args) throws IOException {
    init();
    input();
    operations();
    print();
  }   //  main-end

  private static void init() throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());
    q = Integer.parseInt(tokens.nextToken());
    x = n / m;

    heads = new Node[m + 1];
    tails = new Node[m + 1];

    for(int i = 1; i <= m; i++) {
      heads[i] = new Node(HEAD);
      tails[i] = new Node(TAIL);

      heads[i].next = tails[i];
      tails[i].prev = heads[i];
    }
  }

  private static void input() throws IOException {
    tokens = new StringTokenizer(br.readLine());

    for(int i = 1; i <= n; i++) {
      String name = tokens.nextToken();
      int lineNum = (int)Math.ceil((double)i / x);    //  i번째 사람을 배치할 DLL 번호

      Node last = tails[lineNum].prev;    //  해당 DLL의 마지막 노드
      Node node = new Node(name);
      nodeMap.put(name, node);

      last.next = node;
      node.prev = last;   //  last와 node 연결

      node.next = tails[lineNum];
      tails[lineNum].prev = node; //  node와 tail 연결
    }
  }

  private static void operations() throws IOException {
    for(int i = 0; i < q; i++) {
      tokens = new StringTokenizer(br.readLine());

      int cmd = Integer.parseInt(tokens.nextToken());
      String a = tokens.nextToken();
      String b = cmd == 2 ? null : tokens.nextToken();
      String c = cmd == 3 ? tokens.nextToken() : null;

      switch(cmd) {
        case 1:
          operation1(a, b);
          break;
        case 2:
          operation2(a);
          break;
        case 3:
          operation3(a, b, c);
          break;
      }
    }
  }

  //  name1이라는 이름의 사람이 name2라는 이름의 사람 앞으로 감
  //  name1 노드를 name2 노드 앞에 배치
  private static void operation1(String name1, String name2) {
    Node node1 = nodeMap.get(name1);    //  name1 노드
    Node node2 = nodeMap.get(name2);    //  name2 노드

    if(node2.prev == node1) //  이미 name1 노드가 name2 노드 앞에 있을 경우
      return;

    Node prev1 = node1.prev;    //  node1의 앞의 노드
    Node next1 = node1.next;    //  node1의 뒤의 노드
    Node prev2 = node2.prev;    //  기존 node2의 앞의 노드

    prev1.next = next1;
    next1.prev = prev1; //  node1을 기존 DLL에서 제거

    prev2.next = node1;
    node1.prev = prev2; //  prev2 뒤에 node1 연결
    node1.next = node2;
    node2.prev = node1; //  node1 뒤에 node2 연결
  }

  //  name이라는 이름 가진 사람이 집에 감
  //  name 노드가 있는 DLL에서 그 노드 제거
  private static void operation2(String name) {
    Node outNode = nodeMap.get(name);   //  제거할 노드

    Node prevNode = outNode.prev;   //  이전 노드
    Node nextNode = outNode.next;   //  이후 노드

    prevNode.next = nextNode;
    nextNode.prev = prevNode;
  }

  //  name1이라는 사람부터 name2라는 사람까지
  private static void operation3(String name1, String name2, String name3) {
    Node node1 = nodeMap.get(name1);
    Node node2 = nodeMap.get(name2);
    Node node3 = nodeMap.get(name3);

    Node prev3 = node3.prev;    //  node3 이전 노드
    Node front;
    Node back;   //  node1 .. node2가 이루는 부분 DLL의 가장 앞 노드와 가장 뒤 노드
    Node prev;
    Node next;   //  front의 이전 노드와, back의 이후 노드

    if(isBefore(node1, node2)) {    //  node1이 node2보다 더 앞에 있으면
      front = node1;
      back = node2;
    }
    else {  //  node1이 node2보다 더 뒤에 있으면
      front = node2;
      back = node1;
    }

    prev = front.prev;
    next = back.next;

    prev.next = next;
    next.prev = prev;   //  부분 DLL을 원래 DLL에서 제거

    prev3.next = front;
    front.prev = prev3; //  node3의 앞의 노드 뒤에 부분 DLL의 앞의 노드 연결

    back.next = node3;
    node3.prev = back;  //  node3의 앞에 부분 DLL의 뒤의 노드 연결
  }

  //  node1이 node2보다 head에 가까우면 true
  private static boolean isBefore(Node node1, Node node2) {
    Node cur = node1.prev;

    while(!cur.name.equals(HEAD)) { //  node1의 이전 노드들을 확인
      if(cur == node2)    //  node1의 이전 노드 중에 node2가 있을 경우
        return false;   //  node2가 더 앞섬

      cur = cur.prev;
    }

    return true;    //  node1 앞 노드 중 node2가 없으므로 node1이 더 앞섬
  }

  private static void print() {
    for(int i = 1; i <= m; i++) {
      if(isEmpty(i))
        sb.append("-1\n");
      else {
        Node cur = heads[i].next;

        while(cur != tails[i]) {
          sb.append(cur.name).append(" ");
          cur = cur.next;
        }

        sb.append("\n");
      }
    }

    System.out.print(sb);
  }

  private static boolean isEmpty(int line) {
    return heads[line].next == tails[line];
  }

  private static class Node {
    String name;
    Node prev;  //  이전 노드
    Node next;  //  다음 노드

    public Node(String name) {
      this.name = name;
      this.prev = null;
      this.next = null;
    }
  }
}   //  Main-class-end
```

**CO{)E TREE 최단 거리 9**

[https://www.codetree.ai/missions/8/problems/shortest-distance-9/description](https://www.codetree.ai/missions/8/problems/shortest-distance-9/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/shortest-distance-9/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0

    private lateinit var graph : Array<ArrayList<Node>>
    private lateinit var dist : LongArray
    private lateinit var prev : IntArray
    
    private var a = 0
    private var b = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        graph = Array(n + 1) { ArrayList<Node>() }
        dist = LongArray(n + 1) { Long.MAX_VALUE }
        prev = IntArray(n + 1)

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toLong()

            graph[v1].add(Node(v2, cost))
            graph[v2].add(Node(v1, cost))
        }

        tokens = StringTokenizer(br.readLine())
        a = tokens.nextToken().toInt()
        b = tokens.nextToken().toInt()

        dijkstra()
        path()
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()

        dist[a] = 0
        pq.offer(Node(a, dist[a]))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val cv = cur.v
            val cc = cur.c

            if(dist[cv] < cc)
                continue

            for(next in graph[cv]) {
                val nv = next.v
                val nc = cur.c + next.c

                if(nc < dist[nv]) {
                    dist[nv] = nc
                    pq.offer(Node(nv, dist[nv]))
                    prev[nv] = cv
                }
            }
        }
    }

    private fun path() {
        val stack = Stack<Int>()

        var cur = b

        while(cur != 0) {
            stack.add(cur)
            cur = prev[cur]
        }

        sb.append("${dist[b]}\n")

        while(stack.isNotEmpty())
            sb.append("${stack.pop()} ")
        
        println(sb)
    }

    private class Node(val v : Int, val c : Long) : Comparable<Node> {
        override fun compareTo(other : Node) : Int {
            return this.c.compareTo(other.c)
        }
    }
}

fun main() {
    Main().solve()
}
```