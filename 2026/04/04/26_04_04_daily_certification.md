# 26_04_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22234 가희와 은행

[22234번: 가희와 은행](http://boj.ma/22234/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;
import java.util.PriorityQueue;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //    은행 열기 전 대기한 고객 수
    int t = Integer.parseInt(st.nextToken()); //    고객 한명 한번에 상대하는 시간
    int w = Integer.parseInt(st.nextToken()); //    업무 시간

    Queue<Customer> waitingQueue = new ArrayDeque<>();          //    대기 큐
    PriorityQueue<Customer> afterPq = new PriorityQueue<>();    //    은행 문 열리고 온 고객

    int curTime = 0;

    for(int i = 0; i < n; i++) {
      st = new StringTokenizer(br.readLine());
      int id = Integer.parseInt(st.nextToken());
      int time = Integer.parseInt(st.nextToken());

      waitingQueue.offer(new Customer(id, time, 0));
    }

    //    은행 문 열고 온 고객 수
    int m = Integer.parseInt(br.readLine());

    for(int i = 0; i < m; i++) {
      st = new StringTokenizer(br.readLine());
      int id = Integer.parseInt(st.nextToken());
      int time = Integer.parseInt(st.nextToken());
      int come = Integer.parseInt(st.nextToken());

      afterPq.offer(new Customer(id, time, come));
    }

    while(curTime < w) {
      Customer target = waitingQueue.poll();    //    이번에 업무 볼 고객

      int id = target.id;
      int time = target.time;    //    해당 고객 봐줘야 할 시간
      int maxTime = Math.min(t, w - curTime);    //    실제로 봐줄 수 있는 최대 시간
      int workTime = Math.min(maxTime, time);    //    실제로 고객 봐주는 시간

      target.time -= workTime;    //    해당 고객 업무 봐주기

      //    은행 문 연 이후에 온 고객들 시간에 맞게 대기큐에 넣기
      while(!afterPq.isEmpty() && afterPq.peek().come <= curTime + workTime)
        waitingQueue.offer(afterPq.poll());

      if(target.time > 0)    //    해당 고객 업무가 끝나지 않았을 경우
        waitingQueue.offer(target);    //    다시 대기큐 맨 뒤로 보내기

      for(int i = 0; i < workTime; i++)    //    workTime 동안 처리한 업무 기록
        sb.append(id).append("\n");

      curTime += workTime;
    }

    System.out.print(sb);
  }    //    main-end

  private static class Customer implements Comparable<Customer> {
    public final int id;        //    고객 아이디
    public int time;            //    고객 요구 시간
    public int come;            //    은행 열리고 온 시각

    public Customer(int id, int time, int come) {
      this.id = id;
      this.time = time;
      this.come = come;
    }

    @Override
    public int compareTo(Customer other) {
      return Integer.compare(this.come, other.come);
    }
  }
}    //    Main-class-end
```