# 25_12_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6051 시간 여행

[6051번: 시간 여행](http://boj.ma/6051/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int n = Integer.parseInt(br.readLine());

    SLL sll = new SLL(n + 1);

    for(int queryNum = 1; queryNum <= n; queryNum++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      char cmd = st.nextToken().charAt(0);

      if(cmd == 'a') {  //  푼 문제 추가
        int number = Integer.parseInt(st.nextToken());
        sb.append(sll.insert(queryNum, number)).append("\n");
      }
      else if(cmd == 't') { //  시간여행
        int targetNum = Integer.parseInt(st.nextToken());
        sb.append(sll.timeTravel(queryNum, targetNum - 1)).append("\n");
      }
      else  //  가장 최근에 푼 문제 삭제
        sb.append(sll.delete(queryNum)).append("\n");
    }

    System.out.print(sb);
  }    //    main-end

  private static class Node {
    public int number;  //  문제 번호
    public Node prev;   //  이전 노드

    public Node(int number) {
      this.number = number;
      this.prev = null;
    }
  }

  private static class SLL {
    private Node cur;       //  최종적으로 가리키는 노드
    private final Node[] history; //  history[queryNum] : queryNum번째 질의 결과 가리키는 노드

    public SLL(int n) {
      Node head = new Node(-1); //  빈 노드
      this.history = new Node[n + 1];

      this.history[0] = head;
      this.cur = head;
    }

    public int insert(int queryNum, int number) {
      Node node = new Node(number);

      node.prev = this.cur;
      this.history[queryNum] = node;
      this.cur = node;

      return this.cur.number;
    }

    public int timeTravel(int queryNum, int targetNum) {
      this.cur = this.history[targetNum];
      this.history[queryNum] = this.cur;

      return this.cur.number;
    }

    public int delete(int queryNum) {
      this.cur = this.cur.prev;
      this.history[queryNum] = this.cur;

      return this.cur.number;
    }
  }
}    //    Main-class-end
```