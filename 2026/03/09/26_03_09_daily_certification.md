# 26_03_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 19640 화장실의 규칙

[19640번: 화장실의 규칙](http://boj.ma/19640/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //    사원 수
    int m = Integer.parseInt(st.nextToken()); //    줄 수
    int k = Integer.parseInt(st.nextToken()); //    데카 앞에 선 사람 수 (데카 번호 = K + 1)

    Employee[] employees = new Employee[n + 1];
    PriorityQueue<Employee> pq = new PriorityQueue<>();
    int answer = 0;

    for(int e = 1; e <= n; e++) {
      st = new StringTokenizer(br.readLine());
      int d = Integer.parseInt(st.nextToken());
      int h = Integer.parseInt(st.nextToken());
      int l = e % m == 0 ? m : e % m;

      employees[e] = new Employee(e, d, h, l);

      if(e <= m)    //    최초 M명의 선두 pq에 넣기
        pq.offer(employees[e]);
    }

    while(!pq.isEmpty()) {
      Employee e = pq.poll();    //    선두로 있던 사원들 중 가장 먼저 화장실 갈 사원
      int num = e.num;

      if(num + m <= n) {    //    e가 선두로 있던 줄에서 기다리던 다음 사원이 존재할 경우
        Employee ne = employees[num + m];
        pq.offer(ne);
      }

      if(num == k + 1)    //    이번에 이용한 사원이 데카일 경우
        break;

      answer++;    //    데카보다 앞서 이용한 사람 수 1 증가
    }

    System.out.println(answer);
  }    //    main-end

  private static class Employee implements Comparable<Employee> {
    public int num;  //    사원 번호
    public int d;    //    근무 일수
    public int h;    //    급한 정도
    public int l;    //    줄 번호

    public Employee(int num, int d, int h, int l) {
      this.num = num;
      this.d = d;
      this.h = h;
      this.l = l;
    }

    @Override
    public int compareTo(Employee other) {
      int res = Integer.compare(other.d, this.d);    //    근무 일수가 높을 수록

      if(res == 0)    //    근무 일수가 같을 때
        res = Integer.compare(other.h, this.h);    //    급한 정도가 높을 수록
      if(res == 0)    //    급한 정도도 같을 때
        res = Integer.compare(this.l, other.l);    //    줄 번호가 작을 수록

      return res;
    }
  }
}    //    Main-class-end
```