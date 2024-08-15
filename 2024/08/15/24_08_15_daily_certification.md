# 24_08_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24305 **ОТЧЕТ**

[](https://www.acmicpc.net/problem/24305)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static final Stack<int[]> stack = new Stack<>();
    private static int N;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int cnum = Integer.parseInt(st.nextToken());
            int day = 0;
            
            while(!stack.isEmpty()) {
                int pnum = stack.peek()[0];    //    이전 수
                int pday = stack.peek()[1];    //    이전 수가 있던 날짜
                
                if(pnum < cnum) {
                    day = pday;
                    break;
                }
                
                stack.pop();
            }
            
            sb.append(day).append(" ");
            
            stack.push(new int[] {cnum, i + 1});
        }
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```