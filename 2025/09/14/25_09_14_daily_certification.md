# 25_09_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2591 숫자카드

[2591번: 숫자카드](http://boj.ma/2591/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static char[] input;    //    주어진 숫자 문자열
    private static int N;           //    주어진 숫자 문자열의 길이
    
    //    dp[i] : i ~ N - 1 숫자 문자열을 만들 수 있는 카드 조합 수 (0 <= i < N)
    private static long[] dp;
    
    public static void main(String[] args) throws IOException {
        input = br.readLine().toCharArray();
        N = input.length;
        dp = new long[N];
        
        dp[N - 1] = input[N - 1] == '0' ? 0 : 1;    //    0이 아닌 한 자릿수는 반드시 만들 수 있음
        
        for(int i = N - 2; i >= 0; i--) {
        	if(input[i] == '0')	//	0으로 시작하는 숫자 문자열은 만들 수 없음
        		continue;
        	
            dp[i] = dp[i + 1];    //    i ~ N - 1 숫자 문자열은 (i + 1) ~ N - 1 숫자 문자열 앞에 숫자 하나 추가한 것
            int num = (input[i] - '0') * 10 + input[i + 1] - '0';
            
            if(1 <= num && num <= 34) {
                if(i + 2 == N)
                	dp[i] += 1;
                else
                	dp[i] += dp[i + 2];
            }
        }
        
        System.out.println(dp[0]);
    }    //    main-end
}    //    Main-class-end
```