# 25_10_02_daily_certification
 
# Problem Solving (Algorithm & SQL)

### BOJ 14622 소수 게임

[14622번: 소수 게임](http://boj.ma/14622/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.HashSet;
import java.util.Collections;

public class Main {
    private static final int MAX = 5_000_000;
    private static final int SCORE = 1_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    //    SIEVE[num] : num이 소수일 경우 false
    private static final boolean[] SIEVE = new boolean[MAX + 1];
    
    //    실제로 나온 소수 집합
    private static final Set<Integer> usedSet = new HashSet<>();
    //    대웅이가 말한 소수 리스트 (크기 순)
    private static final PriorityQueue<Integer> pq1 = new PriorityQueue<>(Collections.reverseOrder());    
    //    규성이가 말한 소수 리스트 (크기 순)
    private static final PriorityQueue<Integer> pq2 = new PriorityQueue<>(Collections.reverseOrder());
    
    private static long score1 = 0;    //    대웅 점수
    private static long score2 = 0;    //    규성 점수
    
    public static void main(String[] args) throws IOException {
        init();
        
        N = Integer.parseInt(br.readLine());
        for(int round = 1; round <= N; round++) {
            st = new StringTokenizer(br.readLine());
            int num1 = Integer.parseInt(st.nextToken());    //    대웅이가 수를 먼저 말함
            int num2 = Integer.parseInt(st.nextToken());    //    그 다음에 규성이가 수를 말함
            
            if(SIEVE[num1]) {    //    대웅이가 말한 수가 소수가 아닐 경우
                if(pq2.size() >= 3) {
                    int p1 = pq2.poll();
                    int p2 = pq2.poll();
                    int p3 = pq2.poll();
                    
                    score2 += p3;
                    
                    pq2.offer(p1);
                    pq2.offer(p2);
                    pq2.offer(p3);
                }
                else
                    score2 += SCORE;
            }
            else if(usedSet.contains(num1))    //    대웅이가 이미 말한 소수를 말할 경우
                score1 -= SCORE;
            else {
                pq1.offer(num1);
                usedSet.add(num1);
            }
            
            if(SIEVE[num2]) {    //    규성이가 말한 수가 소수가 아닐 경우
                if(pq1.size() >= 3) {
                    int p1 = pq1.poll();
                    int p2 = pq1.poll();
                    int p3 = pq1.poll();
                    
                    score1 += p3;
                    
                    pq1.offer(p1);
                    pq1.offer(p2);
                    pq1.offer(p3);
                }
                else
                    score1 += SCORE;
            }
            else if(usedSet.contains(num2))    //    규성이가 이미 말한 소수를 말할 경우
                score2 -= SCORE;
            else {
                pq2.offer(num2);
                usedSet.add(num2);
            }
        }
        
        System.out.println(score1 > score2 ? "소수의 신 갓대웅" : (score1 == score2 ? "우열을 가릴 수 없음" : "소수 마스터 갓규성"));
    }    //    main-end
    
    private static void init() {
        SIEVE[0] = true;
        SIEVE[1] = true;
        
        for(int num = 2; num * num <= MAX; num++) {
            if(!SIEVE[num]) {    //    num이 소수일경우
                for(int mult = num * num; mult <= MAX; mult += num)
                    SIEVE[mult] = true;
            }
        }
    }
}    //    Main-class-end
```