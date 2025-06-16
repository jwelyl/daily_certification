# 25_06_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9177 단어 섞기

[9177번: 단어 섞기](http://boj.ma/9177/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;
    private static int N;
    private static int M;
    
    private static char[] word1;
    private static char[] word2;
    private static char[] word3;
    
    //	dp[i1][i2] : word1을 i1 - 1까지 사용, word2를 i2 - 1까지 사용했을 때, word3을 i1 + i2 - 1까지 만들 수 있는 경우의 수
    private static int[][] dp;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            st = new StringTokenizer(br.readLine());
            word1 = st.nextToken().toCharArray();
            word2 = st.nextToken().toCharArray();
            word3 = st.nextToken().toCharArray();
            
            N = word1.length;
            M = word2.length;
            
            dp = new int[N + 1][M + 1];
            dp[0][0] = 1;
            
            for(int i2 = 1; i2 <= M; i2++) {
            	if(word2[i2 - 1] != word3[i2 - 1])
            		break;
            	
            	dp[0][i2] = dp[0][i2 - 1];
            }
            
            for(int i1 = 1; i1 <= N; i1++) {
            	if(word1[i1 - 1] != word3[i1 - 1])
            		break;
            	
            	dp[i1][0] = dp[i1 - 1][0];
            }
            
            for(int i1 = 1; i1 <= N; i1++) {
            	for(int i2 = 1; i2 <= M; i2++) {
            		if(word1[i1 - 1] == word3[i1 + i2 - 1])
            			dp[i1][i2] += dp[i1 - 1][i2];
            		if(word2[i2 - 1] == word3[i1 + i2 - 1])
            			dp[i1][i2] += dp[i1][i2 - 1];
            	}
            }
            
            sb.append("Data set ").append(tc).append(": ").append(dp[N][M] != 0 ? "yes\n" : "no\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end

```