# 25_10_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 27081 Dessert

[http://boj.ma/27081/t](http://boj.ma/27081/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.List;
import java.util.ArrayList;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
    private static final int ADD = Integer.MAX_VALUE;    //    더하기 a + b
    private static final int SUB = Integer.MIN_VALUE;    //    빼기 a - b
    private static final int CON = 0;                    //    접합 a.b => ab
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int N;
    
    private static int[] expression;
    
    private static int answerCnt = 0;    //    정답 개수
    private static final List<String> answers = new ArrayList<>();    //    정답 중 사전순으로 앞서는 20개까지 저장
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        expression = new int[2 * N - 1];
        
        for(int i = 0; i < 2 * N; i += 2)
            expression[i] = i / 2 + 1;
        
        backtracking(1);
        
        for(String answer : answers)
            sb.append(answer).append("\n");
        sb.append(answerCnt);
        
        System.out.println(sb);
    }    //    main-end
    
    private static void backtracking(int nth) {
        if(nth == 2 * N - 1) {
            Deque<Long> deque = new ArrayDeque<>();
            for(int i = 0; i < 2 * N - 1; i++) {
                long elem = expression[i];
                
                if(1 <= elem && elem <= N) {
                    if(!deque.isEmpty() && deque.peekLast() == CON) {
                        deque.pollLast();
                        long prev = deque.pollLast();
                        
                        if(elem < 10)
                            deque.offerLast(prev * 10 + elem);
                        else
                            deque.offerLast(prev * 100 + elem);
                    }
                    else
                        deque.offerLast(elem);
                }
                else
                    deque.offerLast(elem);
            }
            
            long res = deque.pollFirst();
            
            if(!deque.isEmpty()) {
                long opt = deque.pollFirst();
            
                while(!deque.isEmpty()) {
                    long elem = deque.pollFirst();
                    if(elem != ADD && elem != SUB) {
                        if(opt == ADD)
                            res += elem;
                        else
                            res -= elem;
                    }
                    else
                        opt = elem;
                }
            }

            if(res == 0) {
                answerCnt++;
                if(answers.size() < 20) {
                    StringBuilder sb = new StringBuilder();
                    
                    for(int i = 0; i < 2 * N - 1; i++) {
                        int elem = expression[i];

                        if(elem == ADD)
                            sb.append("+ ");
                        else if(elem == SUB)                    
                           sb.append("- ");
                        else if(elem == CON)
                           sb.append(". ");
                        else
                           sb.append(elem + " ");
                    }
                    
                    answers.add(sb.toString());
                }
            }
            
            return;
        }
        
        expression[nth] = ADD;
        backtracking(nth + 2);
        expression[nth] = SUB;
        backtracking(nth + 2);
        expression[nth] = CON;
        backtracking(nth + 2);
    }
}    //    Main-class-end
```