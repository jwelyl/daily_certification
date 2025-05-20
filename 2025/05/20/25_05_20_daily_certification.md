# 25_05_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16854 편안한 문자열

[16854번: 편안한 문자열](http://boj.ma/16854/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    private static int N;
    
    private static char[] input;
    private static boolean[][] dp;
    private static int[] prefixSum;
    
    private static int answer = 0;
	
    public static void main(String[] args) throws IOException {
       input = br.readLine().toCharArray();
       N = input.length;
       
       dp = new boolean[N][N];
       prefixSum = new int[N + 1];
       
       for(int len = 2; len <= N; len++) {
    	   for(int from = 0; from <= N - len; from++) {
    		   int to = from + len - 1;
    		   
    		   if(from + 1 == to)
    			   dp[from][to] = (input[from] != input[to]);
    		   else
    			   dp[from][to] = (input[from] != input[to]) && dp[from + 1][to - 1];
    	   }
       }
       
       for(int i = 1; i <= N; i++) {
    	   char ch = input[i - 1];
    	   
    	   if(ch == '(')
    		   prefixSum[i] = prefixSum[i - 1] + 1;
    	   else
    		   prefixSum[i] = prefixSum[i - 1] - 1;
       }
       
       for(int from = 1; from <= N - 1; from++) {
    	   if(input[from - 1] == ')')
    		   continue;
    	   
    	   for(int to = from + 1; to <= N; to++) {
    		   int ps = prefixSum[to] - prefixSum[from - 1];

    		   if(ps == -1)
    			   break;
    		   
    		   if(ps == 0 && dp[from - 1][to - 1])
    			   answer++;
    	   }
       }
       
       System.out.println(answer);
    }	//	main-end
}	//	Main-class-end
```