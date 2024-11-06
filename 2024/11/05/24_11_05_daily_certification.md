# 24_11_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11000 강의실 배정

[](https://www.acmicpc.net/problem/11000)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();
    private static final List<int[]> lectures = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int l = 0; l < N; l++) {
            st = new StringTokenizer(br.readLine());
            int s = Integer.parseInt(st.nextToken());
            int e = Integer.parseInt(st.nextToken());
            
            lectures.add(new int[] {s, e});
        }
        
        //    시작 시간이 빠른 순으로 정렬, 시작 시간이 같을 경우, 종료 시간이 빠른 순으로 정렬
        Collections.sort(lectures, (l1, l2) -> l1[0] == l2[0] ? Integer.compare(l1[1], l2[1]) : Integer.compare(l1[0], l2[0]));

        for(int[] l : lectures) {
            int s = l[0];
            int e = l[1];
            
            if(!pq.isEmpty() && pq.peek() <= s)
                pq.poll();
            
            pq.offer(e);
        }
    
        System.out.println(pq.size());
    }    //    main-end
}    //    Main-class-end
```