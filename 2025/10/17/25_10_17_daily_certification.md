# 25_10_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23848 등비수열의 합

[23848번: 등비수열의 합](http://boj.ma/23848/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final long MAX_SUM = 1_000_000_000_000L;
    private static final int MAX_RATIO = 1_000_000 - 1;    //    초항 = 1, 항 개수 3일때 최대 공비가 됨
    private static final int MAX_TERMS = (int)(Math.log(MAX_SUM + 1) / Math.log(2));
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static long sum;    //    등비수열의 합
    
    public static void main(String[] args) throws IOException {        
        sum = Long.parseLong(br.readLine());
        
        for(int ratio = 2; ratio <= MAX_RATIO; ratio++) {
            int terms = 3;    //    항 개수
            
            //    sum = (초항 * (ratio ^ terms) - 1) / (ratio - 1)
            //    초항 = (sum * (ratio - 1)) / ((ratio ^ terms) - 1) = child / parent
            long parent = 1L * ratio * ratio * ratio - 1;
            long child = sum * (ratio - 1);
            
            for(; terms <= MAX_TERMS; terms++) {
                if(child % parent == 0) {    //    초항이 정수일 경우
                    long init = child / parent;
                    
                    sb.append(terms).append("\n");
                    for(int i = 0; i < terms; i++) {
                        sb.append(init).append(" ");
                        init *= ratio;
                    }
                    
                    System.out.println(sb);
                    return;
                }
                
                if(parent > MAX_SUM)
                    break;
                
                parent = (parent + 1) * ratio - 1;
            }
        }
        
        System.out.println(-1);
    }    //    main-end
}    //    Main-class-end
```