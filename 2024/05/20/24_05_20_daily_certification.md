# 24_05_20_daily_certification

# Side Project

[https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/와이어프레임?node-id=0-1&t=kZti5PkOLcDyOCoF-0](https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/%EC%99%80%EC%9D%B4%EC%96%B4%ED%94%84%EB%A0%88%EC%9E%84?node-id=0-1&t=kZti5PkOLcDyOCoF-0)

[https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/%EC%99%80%EC%9D%B4%EC%96%B4%ED%94%84%EB%A0%88%EC%9E%84?node-id=0-1&t=kZti5PkOLcDyOCoF-0](https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/%EC%99%80%EC%9D%B4%EC%96%B4%ED%94%84%EB%A0%88%EC%9E%84?node-id=0-1&t=kZti5PkOLcDyOCoF-0)

![IMG_9155.JPG](24_05_20_daily_certification%2089d53f6060564ce5bd1d63717e833868/IMG_9155.jpg)

![IMG_9156.JPG](24_05_20_daily_certification%2089d53f6060564ce5bd1d63717e833868/IMG_9156.jpg)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 테디의 여행 플래너**

[https://www.codetree.ai/missions/8/problems/teddys-travel-planner/description](https://www.codetree.ai/missions/8/problems/teddys-travel-planner/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/teddys-travel-planner/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final String HEAD = "_HEAD";
    private static final String TAIL = "_TAIL";

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  도시 개수
    private static int q;   //  행동 개수

    private static final Node head = new Node(HEAD); //  head dummy node
    private static final Node tail = new Node(TAIL); //  tail dummy node
    private static Node cur = null; // 핀셋 꽂힌 도시
    private static int size = 0;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        q = Integer.parseInt(tokens.nextToken());

        head.next = tail;
        tail.prev = head;
        size = n;

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            Node node = new Node(tokens.nextToken());
            Node pos = tail.prev;   //  pos 뒤에 node를 삽입

            pos.next = node;
            node.prev = pos;    //  pos와 node를 연결
            node.next = tail;
            tail.prev = node;   //  node와 tail을 연결
        }

        cur = head.next;

        for(int i = 0; i < q; i++) {
            tokens = new StringTokenizer(br.readLine());
            int cmd = Integer.parseInt(tokens.nextToken());

            switch(cmd) {
                case 1:
                    operation1();
                    break;
                case 2:
                    operation2();
                    break;
                case 3:
                    operation3();
                    break;
                case 4:
                    String city = tokens.nextToken();
                    operation4(city);
                    break;
            }

            Node left = cur.prev == head ? tail.prev : cur.prev;
            Node right = cur.next == tail ? head.next : cur.next;

            if(left == right || size == 1)
                sb.append("-1\n");
            else
                sb.append(left.city).append(" ").append(right.city).append("\n");
        }

        System.out.print(sb);
    }   //  main-end

    private static void operation1() { 
        if(size == 1)   //  오른쪽 도시가 없을 경우
            return;

        Node right = cur.next == tail ? head.next : cur.next;   //  cur이 가장 마지막 도시면 right는 첫번째 도시
        cur = right;
    }

    private static void operation2() {
        if(size == 1)   //  왼쪽 도시가 없을 경우
            return;

        Node left = cur.prev == head ? tail.prev : cur.prev;    //  cur이 가장 처음 도시면 left는 마지막 도시
        cur = left;
    }

    private static void operation3() {
        if(size == 1)   //  오른쪽 도시가 없을 경우
            return;

        Node right = cur.next == tail ? head.next : cur.next;   //  cur이 가장 마지막 도시면 right는 첫번째 도시
        Node front = right.prev;
        Node back = right.next;

        front.next = back;
        back.prev = front;

        size--;
    }

    private static void operation4(String city) {
        Node insert = new Node(city);

        Node behind = cur.next; //  cur 노드 뒤의 노드
        
        cur.next = insert;
        insert.prev = cur;  //  cur 노드와 insert 노드 연결

        behind.prev = insert;
        insert.next = behind;   //  behind 노드와 insert 노드 연결

        size++;
    }

    private static class Node {
        String city;
        Node prev;
        Node next;

        public Node(String city) {
            this.city = city;
            this.prev = null;
            this.next = null;
        }
    }
}   //  Main-class-end
```

**CO{)E TREE 최단 거리 3**

[https://www.codetree.ai/missions/8/problems/shortest-distance-3/description](https://www.codetree.ai/missions/8/problems/shortest-distance-3/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/shortest-distance-3/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0

    private lateinit var graph : Array<ArrayList<Node>>
    private lateinit var dist : LongArray
    private var a = 0
    private var b = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

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

        tokens = StringTokenizer(br.readLine())
        a = tokens.nextToken().toInt()
        b = tokens.nextToken().toInt()

        dijkstra()

        println(dist[b])
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()
        dist[a] = 0
        pq.offer(Node(a, 0))

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