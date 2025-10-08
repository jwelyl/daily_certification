# 25_10_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 21943 연산 최대로

[21943번: 연산 최대로](http://boj.ma/21943/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
    private static final int ADD = Integer.MAX_VALUE;
    private static final int MUL = Integer.MIN_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    숫자 개수
    
    private static int[] nums;    //    N개 숫자
    
    private static int P;    //    덧셈 개수
    private static int Q;    //    곱셈 개수
    
    private static int[] expression;    //    완성된 식
    
    private static int answer = 0;      //    최댓값
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        nums = new int[N];
        expression = new int[2 * N + 1];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            nums[i] = Integer.parseInt(st.nextToken());
        
        st = new StringTokenizer(br.readLine());
        P = Integer.parseInt(st.nextToken());
        Q = Integer.parseInt(st.nextToken());
        
        bruteforcing(0, new boolean[N], P, Q);

        System.out.println(answer);
    }    //    main-end
    
    private static void bruteforcing(int nth, boolean[] used, int addCnt, int mulCnt) {
        if(nth == 2 * N - 1) {    //    식이 완성된 경우
            Deque<Integer> stack = new ArrayDeque<>();
            
            for(int i = 0; i < 2 * N - 1; i++) {
                int elem = expression[i];
                
                if(!stack.isEmpty() && stack.peekLast() == ADD) {    //    가장 마지막 연산자가 더하기일 경우
                    stack.pollLast();    //    더하기 연산자 꺼내기
                    int opr1 = stack.pollLast();
                    int opr2 = elem;
                    
                    stack.offerLast(opr1 + opr2);    //    더하기 괄호 처리해서 넣기
                }
                else
                    stack.offerLast(elem);
            }
            
            int res = stack.pollLast();
            while(!stack.isEmpty()) {
                stack.pollLast();        //    곱하기 연산자 꺼내기
                res *= stack.pollLast();
            }
            
            answer = Math.max(answer, res);
            
            return;
        }
        
        if(nth % 2 == 0) {    //    수를 채울 차례
            for(int i = 0; i < N; i++) {
                if(!used[i]) {    //    i번째 수를 아직 사용할 수 있을 경우
                    used[i] = true;
                    expression[nth] = nums[i];
                    bruteforcing(nth + 1, used, addCnt, mulCnt);
                    used[i] = false;
                }
            }
        }
        else {    //    연산자 채울 차례
            if(addCnt > 0) {    //    더하기 연산자 남았을 경우
                expression[nth] = ADD;
                bruteforcing(nth + 1, used, addCnt - 1, mulCnt);
            }
            if(mulCnt > 0) {    //    곱하기 연산자 남았을 경우
                expression[nth] = MUL;
                bruteforcing(nth + 1, used, addCnt, mulCnt - 1);
            }
        }
    }
}    //    Main-class-end
```