# 25_04_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2228 구간 나누기

[2228번: 구간 나누기](http://boj.ma/2228/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MOD = 1_000_000;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    private static char[] input;
    
    //	dp[i][0] : i-1번째 숫자만 알파벳으로 변환시켰을때 해석 가능한 경우의 수
    //	dp[i][1] : i-2, i-1번째 숫자를 알파벳으로 변환시켰을때 해석 가능한 경우의 수
    private static int[][] dp;
    
    public static void main(String[] args) throws IOException {
        input = br.readLine().toCharArray();
        N = input.length;
        
        dp = new int[N + 1][2];
        
        if(input[0] != '0') {
        	dp[0][1] = 1;
        	dp[1][0] = 1;
        }
        
        for(int i = 2; i <= N; i++) {
        	int alph = (input[i - 2] - '0') * 10 + input[i - 1] - '0';
        	
        	if(input[i - 1] != '0')	//	i-1번째 숫자를 알파벳으로 변환할 수 있을 경우
        		dp[i][0] = (dp[i - 1][0] % MOD + dp[i - 1][1] % MOD) % MOD;
        	if(10 <= alph && alph <= 26)	//	i-2, i-1번째 숫자를 알파벳으로 변환할 수 있을 경우
        		dp[i][1] = (dp[i - 2][0] % MOD + dp[i - 2][1] % MOD) % MOD;
        }
        
        System.out.println((dp[N][0] % MOD + dp[N][1] % MOD) % MOD);
    }    //    main-end
}    //    Main-class-end
```