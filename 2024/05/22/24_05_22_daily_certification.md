# 24_05_22_daily_certification

# Spring

## OOP

[4. OOP](https://www.notion.so/4-OOP-ae638626c74c49829fc795a82f52ddbc?pvs=21)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 코드트리 유치원**

[https://www.codetree.ai/missions/8/problems/codetree-kindergarden/description](https://www.codetree.ai/missions/8/problems/codetree-kindergarden/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/codetree-kindergarden/description)

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

  private static final Map<Integer, Node> nodeMap = new HashMap<>();
  private static final Node head = new Node(HEAD);
  private static final Node tail = new Node(TAIL);

  private static int q;   //  행동 수
  private static int nextNum = 2; //  다음에 서야 하는 학생 번호

  public static void main(String[] args) throws IOException {
    init();

    q = Integer.parseInt(br.readLine());

    for(int i = 0; i < q; i++) {
      tokens = new StringTokenizer(br.readLine());

      int cmd = Integer.parseInt(tokens.nextToken());
      int a = Integer.parseInt(tokens.nextToken());
      int b = cmd != 3 ? Integer.parseInt(tokens.nextToken()) : 0;

      switch(cmd) {
        case 1:
          pushBack(a, b);
          break;
        case 2:
          pushFront(a, b);
          break;
        case 3:
          print(a);
          break;
      }
    }

    System.out.print(sb);
  }   //  main-end

  private static void init() {
    Node first = new Node(1);

    nodeMap.put(HEAD, head);
    nodeMap.put(TAIL, tail);
    nodeMap.put(1, first);

    head.next = first;
    first.prev = head;  //  1번 노드와 head 연결

    tail.prev = first;
    first.next = tail;  //  1번 노드와 tail 연결
  }

  //  pos 뒤에 cnt개의 노드 삽입
  private static void pushBack(int pos, int cnt) {
    Node posNode = nodeMap.get(pos);    //  삽입할 위치 노드
    Node back = posNode.next;           //  삽입할 위치 노드 뒤의 노드

    Node first = new Node(nextNum);
    nodeMap.put(nextNum++, first);

    Node last = first;

    //  삽입할 노드들을 연결 리스트로 만듬
    for(int i = 0; i < cnt - 1; i++) {  //  first 노드 제외 cnt - 1개 더 삽입해야 함
      Node input = new Node(nextNum);
      nodeMap.put(nextNum++, input);

      last.next = input;
      input.prev = last;
      last = input;
    }

    posNode.next = first;
    first.prev = posNode;   //  삽입할 노드 리스트의 첫번째 노드와 삽입할 위치 노드 연결
    last.next = back;
    back.prev = last;       //  삽입할 노드 리스트의 마지막 노드와 삽입할 위치 노드의 뒤의 노드 연결
  }

  //  pos 앞에 cnt개의 노드 삽입
  private static void pushFront(int pos, int cnt) {
    Node posNode = nodeMap.get(pos);    //  삽입할 위치 노드
    Node front = posNode.prev;           //  삽입할 위치 노드 앞의 노드

    Node first = new Node(nextNum);
    nodeMap.put(nextNum++, first);

    Node last = first;

    //  삽입할 노드들을 연결 리스트로 만듬
    for(int i = 0; i < cnt - 1; i++) {  //  first 노드 제외 cnt - 1개 더 삽입해야 함
      Node input = new Node(nextNum);
      nodeMap.put(nextNum++, input);

      last.next = input;
      input.prev = last;
      last = input;
    }

    posNode.prev = last;
    last.next = posNode;      //  삽입할 노드 리스트의 마지막 노드와 삽입할 위치 노드 연결
    first.prev = front;
    front.next = first;       //  삽입할 노드 리스트의 첫번째 노드와 삽입할 위치 노드의 의 노드 연결
  }

  //  pos 위치 앞, 뒤 노드 번호 출력
  private static void print(int pos) {
    Node posNode = nodeMap.get(pos);
    Node prev = posNode.prev;
    Node next = posNode.next;
    
    sb.append(prev == head || next == tail ? -1 : String.format("%d %d", prev.num, next.num)).append("\n");
  }

  private static class Node {
    int num;
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

**BOJ 2258 중첩 집합 모델**

[19641번: 중첩 집합 모델](https://www.acmicpc.net/problem/19641)

**코드** 

```java
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_2258 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  고기 개수
    private var m = 0   //  필요한 고기 양

    private val meatList = ArrayList<Meat>()

    private var ans = Long.MAX_VALUE    //  m 이상 살 수 없을 경우 -1

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            meatList.add(Meat(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        meatList.sort() //  고기 가격 순으로 정렬

        var totalWeight = meatList[0].weight    //  i번째 고기까지 구매했을 때 누적 무게
        var totalPrice = meatList[0].price      //  i번째 고기까지 구매하기 위한 누적 가격

        for(i in 1 ..< n) {
            if(meatList[i].price == meatList[i - 1].price)  //  i번째 고기가 이전 고기와 가격이 같을 경우
                totalPrice += meatList[i].price //  i번째 고기까지 사기 위해 필요한 비용은 이전 고기까지 누적 비용 + i번째 고기 비용
            else      //  i번째 고기가 이전 고기보다 비쌀 경우
                totalPrice = meatList[i].price  //  i번째 고기까지 사면 이전 고기는 공짜로 얻을 수 있음

            totalWeight += meatList[i].weight

            if(totalWeight >= m)
                ans = ans.coerceAtMost(totalPrice.toLong())
        }

        println(if(ans == Long.MAX_VALUE) -1 else ans)
    }

    private class Meat(val weight : Int, val price : Int) : Comparable<Meat> {
        override fun compareTo(other : Meat) : Int {
            var ret = this.price.compareTo(other.price) //  가격 순 오름차순

            if(ret == 0)
                ret = other.weight.compareTo(this.weight)   //  가격 같을 경우 무게 순 내림차순

            return ret
        }
    }
}

fun main() {
    BOJ_2258().solve()
}
```