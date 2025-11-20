# 25_11_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23090 난민

[23090번: 난민](http://boj.ma/23090/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;
import java.util.Collections;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringBuilder sb = new StringBuilder();
        StringTokenizer st;
        
        MedianPQ pq = new MedianPQ();
        
        int n;                //    난민촌 개수
        long xSum = 0;        //    난민촌 증가할 때마다 증가하는 x좌표 차이 합
        
        n = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < n; i++) {
            st = new StringTokenizer(br.readLine());
            int x = Integer.parseInt(st.nextToken());
            int y = Integer.parseInt(st.nextToken());
            
            xSum += Math.abs(x);
            pq.offer(y);
            
            int median = pq.peek();
            long ySum = pq.minYDistSum();
            
            sb.append(median).append(" ").append(xSum + ySum).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static class MedianPQ {
        private final PriorityQueue<Integer> maxPQ = new PriorityQueue<>(Collections.reverseOrder());
        private final PriorityQueue<Integer> minPQ = new PriorityQueue<>();
        
        private long maxSum = 0;
        private long minSum = 0;
        
        public void offer(int element) {
            //    maxPQ와 minPQ에 저장된 원소 개수가 같을 경우, maxPQ에 저장될 차례
            if(this.maxPQ.size() == this.minPQ.size()) {
                if(this.minPQ.isEmpty()) {   //    비어있을 경우 그냥 바로 maxPQ에 넣으면 됨
                    this.maxPQ.offer(element);
                    this.maxSum += element;
                }
                else {
                    int min = minPQ.peek();    //    minPQ에 저장된 가장 작은 원소
                    
                    if(element > min) {        //    이번에 삽입될 원소가 min보다 클 경우
                        minPQ.poll();          //    min을 minPQ에서 제거
                        this.minSum -= min;
                        maxPQ.offer(min);      //    maxPQ에 min을 대신 삽입
                        this.maxSum += min;
                        minPQ.offer(element);  //    element를 minPQ에 저장
                        this.minSum += element;
                    }
                    else {
                        maxPQ.offer(element);
                        this.maxSum += element;
                    }
                }
            }
            else {    //    maxPQ가 1개 더 많을 경우, minPQ에 저장해야 함
                int max = maxPQ.peek();    //    maxPQ에 저장된 가장 큰 원소
                
                if(element < max) {        //    이번에 삽입될 원소가 max보다 작을 경우
                    maxPQ.poll();          //    max를 maxPQ에서 제거
                    this.maxSum -= max;
                    minPQ.offer(max);      //    minPQ에 max를 대신 삽입
                    this.minSum += max;
                    maxPQ.offer(element);  //    element를 maxPQ에 저장
                    this.maxSum += element;
                }
                else {
                    minPQ.offer(element);
                    this.minSum += element;
                }
            }
        }
        
        public long minYDistSum() {
            int median = this.peek();
            long leftCost = (long)median * maxPQ.size() - maxSum;
            long rightCost = minSum - (long)median * minPQ.size();
            
            return leftCost + rightCost;
        }
        
        public int peek() {
            return this.maxPQ.peek();
        }
    }
}    //    Main-class-end
```