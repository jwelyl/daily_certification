# 25_10_28_daily_certification

# To Do List

# Problem Solving (Algorithm & SQL)

### 프로그래머스 큰 수 만들기

[](https://school.programmers.co.kr/learn/courses/30/lessons/42883)

```java
import java.util.Deque;
import java.util.ArrayDeque;

class Solution {
    private final Deque<Character> deque = new ArrayDeque<>();
    
    public String solution(String number, int k) {
        StringBuilder sb = new StringBuilder();
        
        deque.offer((char)('0' + 10));
        
        for(int i = 0; i < number.length(); i++) {
            char digit = number.charAt(i);
            
            while(deque.peekLast() < digit && k > 0) {
                deque.pollLast();
                k--;
            }    
            
            deque.offerLast(digit);
        }
        
        while(k > 0) {
            deque.pollLast();
            k--;
        }
        
        deque.pollFirst();
        while(!deque.isEmpty())
            sb.append(deque.pollFirst());
        
        return sb.toString();
    }
}
```

### BOJ 2812 크게 만들기

[2812번: 크게 만들기](http://boj.ma/2812/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;        //    최초 자릿수
    private static int K;        //    지울 자릿수
    private static char[] num;   //    최초에 주어진 수
    
    private static final Deque<Character> deque = new ArrayDeque<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        num = br.readLine().toCharArray();
        deque.offerLast((char)('0' + 10));
        
        for(char digit : num) {
            while(deque.peekLast() < digit && K > 0) {
                deque.pollLast();
                K--;
            }
            
            deque.offerLast(digit);
        }
        
        deque.pollFirst();
        while(K > 0) {
            deque.pollLast();
            K--;
        }
        
        while(!deque.isEmpty())
            sb.append(deque.pollFirst());
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```