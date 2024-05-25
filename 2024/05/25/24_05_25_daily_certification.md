# 24_05_25_daily_certification

# Spring

## OOP

[4. OOP](https://www.notion.so/4-OOP-ae638626c74c49829fc795a82f52ddbc?pvs=21)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 강강수월래**

[https://www.codetree.ai/missions/8/problems/circle-dance/description](https://www.codetree.ai/missions/8/problems/circle-dance/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/circle-dance/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    //  Key : 노드 번호, Value : 해당 노드
    private static final Map<Integer, Node> nodeMap = new HashMap<>();

    private static int n;   //  학생 수
    private static int m;   //  원의 수
    private static int q;   //  동작 수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());

        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());
        q = Integer.parseInt(tokens.nextToken());

        for(int i = 0; i < m; i++) {
            tokens = new StringTokenizer(br.readLine());

            int cnt = Integer.parseInt(tokens.nextToken()); //  i번째 CLL에 있는 학생 수
            Node first = null;  //  첫 번째 노드

            if(cnt == 1) {
                int num = Integer.parseInt(tokens.nextToken());

                first = new Node(num);
                first.prev = first;
                first.next = first;

                nodeMap.put(num, first);
            }
            else if(cnt > 1) {
                int num = Integer.parseInt(tokens.nextToken());

                first = new Node(num);
                nodeMap.put(num, first);

                Node last = first;  //  DLL의 마지막 노드

                for(int j = 0; j < cnt - 1; j++) {
                    num = Integer.parseInt(tokens.nextToken());
                    Node cur = new Node(num);
                    nodeMap.put(num, cur);

                    last.next = cur;
                    cur.prev = last;    //  마지막 노드와 현재 노드 연결

                    last = cur; //  마지막 노드 갱신
                }

                first.prev = last;
                last.next = first;    //  첫 번째 노드의 이전 노드를 마지막 노드로, 마지막 노드의 다음 노드를 첫 번째 노드로 CLL 구성
            }
        }

        for(int i = 0; i < q; i++) {
            tokens = new StringTokenizer(br.readLine());
            int cmd = Integer.parseInt(tokens.nextToken());
            int a = Integer.parseInt(tokens.nextToken());
            int b = (cmd == 3) ? 0 : Integer.parseInt(tokens.nextToken());

            switch(cmd) {
                case 1:
                    operation1(a, b);
                    break;
                case 2:
                    operation2(a, b);
                    break;
                case 3:
                    operation3(a);
                    break;
            }
        }
    }   //  main-end

    //  서로 다른 CLL에 존재하는 a라는 수를 가진 노드와 b라는 수를 가진 노드를 연결해서 두 CLL을 합치기
    private static void operation1(int a, int b) {
        Node nodeA = nodeMap.get(a);
        Node nodeB = nodeMap.get(b);

        Node nextA = nodeA.next;    //  nodeA의 다음 노드
        Node prevB = nodeB.prev;    //  nodeB의 이전 노드

        nodeA.next = nodeB;
        nodeB.prev = nodeA;

        nextA.prev = prevB;
        prevB.next = nextA;
    }

    //  a라는 수를 가진 노드부터 시작해서 b라는 수를 가진 노드 전까지 시계 방향으로 탐색하여 분리해냄
    private static void operation2(int a, int b) {
        Node nodeA = nodeMap.get(a);
        Node nodeB = nodeMap.get(b);

        Node prevA = nodeA.prev;    //  nodeA의 이전 노드
        Node prevB = nodeB.prev;    //  nodeB의 이전 노드

        nodeA.prev = prevB;
        prevB.next = nodeA; //  nodeA와 nodeB의 이전 노드를 연결해서 CLL로 만듬

        prevA.next = nodeB;
        nodeB.prev = prevA; //  nodeB와 nodeA의 이전 노드를 연결해서 CLL로 만듬
    }

    //  a라는 수를 가진 노드가 포함된 CLL에서 번호가 가장 작은 노드를 찾음
    //  해당 노드부터 시계 반대 방향으로 자기 번호 출력하기
    private static void operation3(int a) {
        Node nodeA = nodeMap.get(a);        //  a라는 번호 가진 노드
        int minNum = a;                     //  해당 CLL의 최소 번호
        Node minNode = nodeA;               //  최소 번호를 가지는 노드
        Node cur = nodeA.next;

        while(cur.num != a) {   //  nodeA 다음 노드부터 시계 방향으로 한 바퀴 순회하기
            if(cur.num < minNum) {
                minNum = cur.num;
                minNode = cur;      //  최소 번호를 가지는 노드 갱신
            }
        
            cur = cur.next;
        }

        sb.append(minNum).append(" ");
        
        cur = minNode.prev;

        while(cur.num != minNum) {  //  minNode 이전부터 시계 반대 방향으로 한 바퀴 순회하기
            sb.append(cur.num).append(" ");
            cur = cur.prev; 
        }

        System.out.print(sb);
    }

    private static class Node {
        int num;
        Node prev;  //  시계 반대 방향으로 이전 노드
        Node next;  //  시계 방향으로 다음 노드

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