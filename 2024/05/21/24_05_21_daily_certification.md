# 24_05_21_daily_certification

# Spring

## OOP

[4. OOP](https://www.notion.so/4-OOP-ae638626c74c49829fc795a82f52ddbc?pvs=21)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 기사 출정식**

[https://www.codetree.ai/missions/8/problems/knights-commencement/description](https://www.codetree.ai/missions/8/problems/knights-commencement/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/knights-commencement/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final int HEAD = 0;
  private static final int TAIL = Integer.MAX_VALUE;

  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;

  private static Map<Integer, Node> nodeMap = new HashMap<>();    //  Key : 노드 번호, Value : 노드

  private static Node head = new Node(HEAD);
  private static Node tail = new Node(TAIL);

  private static int n;   //  노드 개수
  private static int m;   //  노드 제거 횟수

  public static void main(String[] args) throws IOException {
    init();
    query();

    System.out.print(sb);
  }   //  main-end

  private static void init() throws IOException {
    head.next = tail;
    tail.prev = head;

    nodeMap.put(HEAD, head);
    nodeMap.put(TAIL, tail);

    tokens = new StringTokenizer(br.readLine());
    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    tokens = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++) {
      int num = Integer.parseInt(tokens.nextToken());
      Node node = new Node(num);
      nodeMap.put(num, node);

      Node front = tail.prev; //  현재 가장 마지막 노드
      front.next = node;
      node.prev = front;  //  node와 마지막 노드를 연결

      node.next = tail;
      tail.prev = node;   //  node와 tail을 연결
    }
  }

  private static void query() throws IOException {
    for(int i = 0; i < m; i++)
      delete(Integer.parseInt(br.readLine()));
  }

  private static void delete(int num) {
    Node delNode = nodeMap.get(num);    //  제거할 노드
    Node left = delNode.next.num == TAIL ? head.next : delNode.next;   //  제거할 노드가 가장 뒤의 노드일 경우 가장 처음 노드가 왼쪽 노드
    Node right = delNode.prev.num == HEAD ? tail.prev : delNode.prev;    //  제거할 노드가 가장 앞의 노드일 경우 가장 마지막 노드가 오른쪽 노드

    right.next = left;
    left.prev = right;

    sb.append(left.num).append(" ").append(right.num).append("\n");
  }

  private static class Node {
    int num;    //  기사 번호
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

**CO{)E TREE 각 정점까지의 최단 경로**

[https://www.codetree.ai/missions/8/problems/shortest-path-to-each-vertex/description](https://www.codetree.ai/missions/8/problems/shortest-path-to-each-vertex/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/shortest-path-to-each-vertex/description)

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
    private var k = 0

    private lateinit var graph : Array<ArrayList<Node>>
    private lateinit var dist : LongArray

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        k = br.readLine().toInt()

        graph = Array(n + 1) { ArrayList<Node>() }
        dist = LongArray(n + 1) { Long.MAX_VALUE }

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val c = tokens.nextToken().toLong()

            graph[v1].add(Node(v2, c))
            graph[v2].add(Node(v1, c))
        }

        dijkstra()

        for(i in 1 .. n)
            sb.append("${if(dist[i] == Long.MAX_VALUE) -1 else dist[i]}\n")

        print(sb)
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()
        dist[k] = 0
        pq.offer(Node(k, 0))

        while(pq.isNotEmpty()) {
            val cNode = pq.poll()
            val cVertex = cNode.vertex
            val cCost = cNode.cost

            if(cCost > dist[cVertex])   //  cVertex의 기존에 알려진 최단거리보다 클 경우 가치가 없음
                continue

            for(nNode in graph[cVertex]) {
                val nVertex = nNode.vertex
                val nCost = cCost + nNode.cost  //  nVertex까지 cVertex를 거쳐서 갈 경우의 거리

                if(dist[nVertex] > nCost) { //  cVertex를 거쳐서 가는게 더 거리가 짧을 경우
                    dist[nVertex] = nCost   //  nVertex까지의 최단거리 갱신
                    pq.offer(Node(nVertex, dist[nVertex]))
                }
            }
        }
    }   //  dijkstra-end

    private class Node(val vertex : Int, val cost : Long) : Comparable<Node> {
        override fun compareTo(other : Node) : Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    Main().solve()
}
```