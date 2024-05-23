# 24_05_23_daily_certification

# Spring

## OOP

[4. OOP](https://www.notion.so/4-OOP-ae638626c74c49829fc795a82f52ddbc?pvs=21)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 새치기**

[https://www.codetree.ai/missions/8/problems/cut-in-line/description](https://www.codetree.ai/missions/8/problems/cut-in-line/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/cut-in-line/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;

  private static final Map<Integer, Node> nodeMap = new HashMap<>();  //  Key : 사람 번호, Value : 해당 노드

  private static int n;   //  사람 수
  private static int m;   //  줄 수
  private static int q;   //  문자 수

  private static Node[] heads;
  private static Node[] tails;

  public static void main(String[] args) throws IOException {
    init();

    for(int i = 0; i < q; i++) {
      tokens = new StringTokenizer(br.readLine());

      int cmd = Integer.parseInt(tokens.nextToken());
      int a = Integer.parseInt(tokens.nextToken());
      int b = cmd == 2 ? 0 : Integer.parseInt(tokens.nextToken());
      int c = cmd == 3 ? Integer.parseInt(tokens.nextToken()) : 0;

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

    print();
  }   //  main-end

  private static void init() throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());
    q = Integer.parseInt(tokens.nextToken());

    heads = new Node[m + 1];
    tails = new Node[m + 1];

    for(int i = 1; i <= m; i++) {
      heads[i] = new Node(0);
      tails[i] = new Node(Integer.MAX_VALUE);

      heads[i].next = tails[i];
      tails[i].prev = heads[i];
    }

    for(int i = 1; i <= m; i++) {
      tokens = new StringTokenizer(br.readLine());

      int cnt = Integer.parseInt(tokens.nextToken()); //  i번째 줄에 선 사람 수

      for(int j = 0; j < cnt; j++) {
        int num = Integer.parseInt(tokens.nextToken());

        Node node = new Node(num);
        nodeMap.put(num, node);

        Node rear = tails[i].prev; //  i번째 연결 리스트의 가장 마지막 노드
        rear.next = node;
        node.prev = rear;   //  rear와 node 연결

        node.next = tails[i];
        tails[i].prev = node; //  node와 tail 연결
      }
    }
  }

  //  num1인 사람이 num2인 사람 앞으로 옴
  private static void operation1(int num1, int num2) {
    Node node1 = operation2(num1);

    Node node2 = nodeMap.get(num2);
    Node front2 = node2.prev;   //  node2의 앞 노드

    node1.prev = front2;
    front2.next = node1;    //  front2 뒤에 node1 연결

    node1.next = node2;
    node2.prev = node1;     //  node2 앞에 node1 연결
  }

  //  num인 사람이 줄에서 이탈시키고 그 노드를 반환함
  private static Node operation2(int num) {
    Node node = nodeMap.get(num);

    Node front = node.prev;         //  node의 앞 노드
    Node back = node.next;          //  node의 뒷 노드

    front.next = back;
    back.prev = front;      //  node를 현재 위치에서 이탈시킴

    return node;
  }

  //  num1인 사람부터 num2인 사람까지 순서대로 num3인 사람 앞으로 새치기함
  private static void operation3(int num1, int num2, int num3) {
    Node node1 = nodeMap.get(num1);
    Node node2 = nodeMap.get(num2);

    Node front1 = node1.prev;
    Node back2 = node2.next;

    front1.next = back2;
    back2.prev = front1;    //  node1부터 node2까지의 부분 연결 리스트를 전체 연결 리스트에서 이탈시킴

    Node node3 = nodeMap.get(num3);
    Node front3 = node3.prev;   //  node3의 앞 노드

    front3.next = node1;
    node1.prev = front3;    //  front3 뒤에 node1 위치

    node2.next = node3;
    node3.prev = node2;     //  node2 뒤에 node3 위치
  }

  //  nth번째 연결 리스트가 비었으면 true
  private static boolean isEmpty(int nth) {
    return heads[nth].next == tails[nth];
  }

  private static void print() {
    for(int i = 1; i <= m; i++) {
      if(isEmpty(i))
        sb.append("-1\n");
      else {
        Node cur = heads[i].next;

        while(cur != tails[i]) {
          sb.append(cur.num).append(" ");
          cur = cur.next;
        }

        sb.append("\n");
      }
    }

    System.out.print(sb);
  }

  private static class Node {
    int num;    //  사람 번호
    Node prev;
    Node next;

    public Node(int num) {
      this.num = num;
      this.prev = null;
      this.next = null;
    }
  }
}   //  Main-class-end
```

**CO{)E TREE 가장 오래 걸리는 학생 2**

[https://www.codetree.ai/missions/8/problems/longest-student-2/description](https://www.codetree.ai/missions/8/problems/longest-student-2/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0   //  정점 개수
    private var m = 0   //  간선 개수

    private lateinit var dist : IntArray
    private lateinit var graph : Array<ArrayList<Node>>

    private var ans = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        dist = IntArray(n + 1) { Int.MAX_VALUE }
        graph = Array(n + 1 ) { ArrayList<Node>() }

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toInt()

            graph[v2].add(Node(v1, cost))
        }

        dijkstra()

        for(i in 1 .. n)
            ans = ans.coerceAtLeast(dist[i])

        println(ans)
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()
        dist[n] = 0
        pq.offer(Node(n, dist[n]))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val cv = cur.vertex
            val cc = cur.cost

            if(cc > dist[cv])
                continue

            for(next in graph[cv]) {
                val nv = next.vertex
                val nc = cc + next.cost

                if(nc < dist[nv]) {
                    dist[nv] = nc
                    pq.offer(Node(nv, dist[nv]))
                }
            }
        }
    }

    private class Node(val vertex : Int, val cost : Int) : Comparable<Node> {
        override fun compareTo(other : Node) : Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    Main().solve()
}
```