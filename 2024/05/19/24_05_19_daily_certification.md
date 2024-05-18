# 24_05_19_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 책꽂이 정리**

[https://www.codetree.ai/missions/8/problems/bookshelf-clean/description](https://www.codetree.ai/missions/8/problems/bookshelf-clean/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/bookshelf-clean/description)

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

  private static Node[] heads;    //  heads[i] : i번째 연결 리스트의 head 더미 노드
  private static Node[] tails;    //  tails[i] : i번째 연결 리스트의 tail 더미 노드
  private static int[] cnts;      //  cnts[i] : i번째 연결 리스트의 더미 노드를 제외한 노드 개수

  private static int n;   //  책 개수
  private static int k;   //  책꽂이 개수
  private static int q;   //  쿼리 개수

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());
    n = Integer.parseInt(tokens.nextToken());
    k = Integer.parseInt(tokens.nextToken());

    init();

    q = Integer.parseInt(br.readLine());

    for(int i = 0; i < q; i++) {
      tokens = new StringTokenizer(br.readLine());

      int cmd = Integer.parseInt(tokens.nextToken());
      int num1 = Integer.parseInt(tokens.nextToken());
      int num2 = Integer.parseInt(tokens.nextToken());

      switch(cmd) {
        case 1:
          operation1(num1, num2);
          break;
        case 2:
          operation2(num1, num2);
          break;
        case 3:
          operation3(num1, num2);
          break;
        case 4:
          operation4(num1, num2);
          break;
      }
    }

    print();
  }   //  main-end

  private static void init() {
    heads = new Node[k + 1];
    tails = new Node[k + 1];
    cnts = new int[k + 1];

    for(int i = 1; i <= k; i++) {
      heads[i] = new Node(HEAD);
      tails[i] = new Node(TAIL);  //  각 연결 리스트의 head, tail 생성

      heads[i].next = tails[i];
      tails[i].prev = heads[i];   //  head, tail 서로 연결
    }

    for(int i = 1; i <= n; i++) {
      Node node = new Node(i);

      Node pos = tails[1].prev;
      pos.next = node;
      node.prev = pos;
      tails[1].prev = node;
      node.next = tails[1];   //  1번 연결 리스트의 가장 뒤에 n개의 노드 삽입
    }

    cnts[1] = n;
  }

  //  i번째 연결 리스트의 가장 앞의 노드를 빼서 j번째 연결 리스트의 가장 뒤에 넣음
  private static void operation1(int i, int j) {
    Node iFront = heads[i].next;    //  i번째 연결 리스트의 가장 앞의 노드

    if(iFront.num == TAIL)  //  가장 앞의 노드가 tail이라면 빈 연결 리스트
      return;

    Node iNext = iFront.next;   //  i번째 연결 리스트의 두번째 노드
    heads[i].next = iNext;
    iNext.prev = heads[i];  //  iFront를 i번째 연결 리스트에서 제거
    cnts[i]--;

    Node jBack = tails[j].prev; //  j번째 연결 리스트의 가장 뒤의 노드
    jBack.next = iFront;
    iFront.prev = jBack;        //  jBack과 iFront 연결
    tails[j].prev = iFront;
    iFront.next = tails[j];     //  iFront와 tail 연결
    cnts[j]++;
  }

  //  i번째 연결 리스트의 가장 뒤의 노드를 빼서 j번째 연결 리스트의 가장 앞에 넣음
  private static void operation2(int i, int j) {
    Node iBack = tails[i].prev;    //  i번째 연결 리스트의 가장 뒤의 노드

    if(iBack.num == HEAD)  //  가장 앞의 노드가 head라면 빈 연결 리스트
      return;

    Node iPrev = iBack.prev;   //  i번째 연결 리스트의 뒤에서 두번째 노드
    tails[i].prev = iPrev;
    iPrev.next = tails[i];  //  iBack을 i번째 연결 리스트에서 제거
    cnts[i]--;

    Node jFront = heads[j].next; //  j번째 연결 리스트의 가장 앞의 노드
    jFront.prev = iBack;
    iBack.next = jFront;        //  jFront과 iBack 연결
    heads[j].next = iBack;
    iBack.prev = heads[j];     //  iBack와 head 연결
    cnts[j]++;
  }

  //  i번째 연결 리스트의 모든 노드를 j번 연결 리스트의 가장 앞으로 옮기기
  private static void operation3(int i, int j) {
    if(i == j)  return;

    Node iStart = heads[i].next;  //  i번째 연결 리스트의 가장 앞의 노드

    if(iStart.num == TAIL)  //  가장 앞의 노드가 tail이라면 빈 연결 리스트
      return;

    Node iEnd = tails[i].prev;      //  i번째 연결 리스트의 가장 뒤의 노드
    Node jStart = heads[j].next;    //  j번째 연결 리스트의 가장 앞의 노드

    heads[j].next = iStart;
    iStart.prev = heads[j]; //  iStart와 head 연결
    iEnd.next = jStart;
    jStart.prev = iEnd;     //  iEnd와 jStart의 연결

    heads[i].next = tails[i];
    tails[i].prev = heads[i];   //  i번째 연결 리스트의 head와 tail 연결

    cnts[j] += cnts[i];
    cnts[i] = 0;
  }

  //  i번째 연결 리스트의 모든 노드를 j번 연결 리스트의 가장 뒤로 옮기기
  private static void operation4(int i, int j) {
    if(i == j)  return;

    Node iStart = heads[i].next;  //  i번째 연결 리스트의 가장 앞의 노드

    if(iStart.num == TAIL)  //  가장 앞의 노드가 tail이라면 빈 연결 리스트
      return;

    Node iEnd = tails[i].prev;      //  i번째 연결 리스트의 가장 뒤의 노드
    Node jEnd = tails[j].prev;      //  j번째 연결 리스트의 가장 뒤의 노드

    jEnd.next = iStart;
    iStart.prev = jEnd;     //  iStart와 jEnd의 연결
    tails[j].prev = iEnd;
    iEnd.next = tails[j];  //  iEnd와 tail의 연결

    heads[i].next = tails[i];
    tails[i].prev = heads[i];   //  i번째 연결 리스트의 head와 tail 연결

    cnts[j] += cnts[i];
    cnts[i] = 0;
  }

  private static void print() {
    for(int i = 1; i <= k; i++) {
      Node cur = heads[i].next;

      sb.append(cnts[i]).append(" ");

      while(cur.num != TAIL) {
        sb.append(cur.num).append(" ");
        cur = cur.next;
      }

      sb.append("\n");
    }

    System.out.print(sb);
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

**CO{)E TREE 숫자의 개수**

[https://www.codetree.ai/missions/8/problems/number-of-integers/description](https://www.codetree.ai/missions/8/problems/number-of-integers/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/number-of-integers/description)

**코드** 

```kotlin
import java.util.*
import java.io.*
import java.lang.StringBuilder
import kotlin.collections.ArrayList

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer
    private val sb = StringBuilder()

    private var n = 0   //  자연수 개수
    private var m = 0   //  질의 개수

    private val nums = ArrayList<Int>()

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        tokens = StringTokenizer(br.readLine())
        repeat(n) {
            nums.add(tokens.nextToken().toInt())
        }

        repeat(m) {
            val target = br.readLine().toInt()

            val lower = lowerBound(target)  //  nums에서 target이 가장 처음에 나타나는 위치
            val upper = upperBound(target)  //  nums에서 target이 가장 마지막에 나타나는 위치

            sb.append("${if(lower == -1 || upper == -1) 0 else upper - lower + 1}\n")
        }

        print(sb)
    }

    private fun lowerBound(target : Int) : Int {
        var ret = -1    //  존재하지 않으면 -1

        var start = 0
        var end = n - 1

        while(start <= end) {
            val mid = (start + end) / 2

            if(nums[mid] == target) {   //  찾은 경우
                ret = mid
                end = mid - 1;  //  더 왼쪽에서 찾아보기
            }
            else if(nums[mid] < target) //  더 작은 경우
                start = mid + 1 //  더 오른쪽에서 찾아보기
            else    //  더 큰 경우
                end = mid - 1   //  더 왼쪽에서 찾아보기

        }

        return ret
    }

    private fun upperBound(target : Int) : Int {
        var ret = -1    //  존재하지 않으면 -1

        var start = 0
        var end = n - 1

        while(start <= end) {
            val mid = (start + end) / 2

            if(nums[mid] == target) {   //  찾은 경우
                ret = mid
                start = mid + 1;  //  더 오른쪽에서 찾아보기
            }
            else if(nums[mid] < target) //  더 작은 경우
                start = mid + 1 //  더 오른쪽에서 찾아보기
            else    //  더 큰 경우
                end = mid - 1   //  더 왼쪽에서 찾아보기

        }

        return ret
    }

}

fun main() {
    Main().solve()
}
```