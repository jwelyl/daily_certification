# 25_09_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17612 쇼핑몰 (Priority Queue)

[17612번: 쇼핑몰](http://boj.ma/17612/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    고객 수
    private static int K;    //    카운터 수
    
    private static Customer[] customers;
    private static final PriorityQueue<Counter> counterPq = new PriorityQueue<>();
    private static final PriorityQueue<Customer> customerPq = new PriorityQueue<>();
    
    private static int order = 1;
    private static int[] outs;    //    outs[nth] : nth번째로 나간 고객 번호
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        customers = new Customer[N + 1];
        for(int nth = 1; nth <= N; nth++) {
            st = new StringTokenizer(br.readLine());
            int id = Integer.parseInt(st.nextToken());
            int w = Integer.parseInt(st.nextToken());
            
            customers[nth] = new Customer(id, w);
        }
        outs = new int[N + 1];
        
        for(int num = 1; num <= K; num++)
            counterPq.offer(new Counter(num));
        
        for(int nth = 1; nth <= N; nth++) {
            Customer customer = customers[nth];            //    계산해야 할 고객
            Counter counter = counterPq.poll();            //    해당 고객을 계산할 카운터
            
            customer.counter = counter.num;                         //    고객이 계산한 카운터 번호 기입
            customer.endTime = counter.time + customer.w;           //    고객이 계산 끝난 시간
            counter.time += customer.w;                             //    해당 카운터가 손님 받을 수 있는 시간이 w 시간 늦춰짐
        
            customerPq.offer(customer);
            counterPq.offer(counter);
        }
        
        while(!customerPq.isEmpty()) {
            Customer customer = customerPq.poll();
            outs[order++] = customer.id;
        }
        
        for(int i = 1; i <= N; i++)
            answer += (long)outs[i] * i;
        
        System.out.println(answer);
    }    //    main-end
    
    private static class Counter implements Comparable<Counter> {
        public final int num;    //    카운터 번호
        public int time;         //    고객 받을 수 있는 시간, 현재 시각이 time 이후면 고객 받을 수 있음
        
        public Counter(int num) {
            this.num = num;
            this.time = 0;
        }
        
        @Override
        public int compareTo(Counter other) {
            int res = Integer.compare(this.time, other.time);    //    고객을 빨리 받을 수 있는 카운터 우선
        
            if(res == 0)    //    고객을 받을 수 있는 카운터가 여럿일 경우
                res = Integer.compare(this.num, other.num);    //    번호 빠른 카운터 우선
            
            return res;
        }
    }
    
    private static class Customer implements Comparable<Customer> {
        public final int id;    //    고객 id
        public final int w;     //    고객이 산 물건 개수
        public int counter;     //    고객이 계산한 계산대
        public int endTime;     //    고객이 계산 마친 시간
        
        public Customer(int id, int w) {
            this.id = id;
            this.w = w;
        }
        
        @Override
        public int compareTo(Customer other) {
            int res = Integer.compare(this.endTime, other.endTime);    //    계산 먼저 끝난 고객이 먼저 나감
            if(res == 0)    //    동시에 끝난 고객이 있을 경우
                res = Integer.compare(other.counter, this.counter);    //    계산대 번호가 큰 고객 먼저 나감
            
            return res;
        }
    }
}    //    Main-class-end
```