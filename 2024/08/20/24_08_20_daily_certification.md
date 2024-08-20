# 24_08_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1248 Guess

[](https://www.acmicpc.net/problem/1248)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int N;            //    길이
    private static char[][] smap;    //    s값 저장
    private static int[][] dp;
    private static int[] res;
    
    private static boolean find = false;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        smap = new char[N][N];
        dp = new int[N][N];
        res = new int[N];
        
        char[] input = br.readLine().toCharArray();
        int idx = 0;
        
        for(int cnt = N; cnt >= 1; cnt--) {
            int start = N - cnt;
            
            for(int i = start; i < N; i++)
                smap[start][i] = input[idx++];
        }
        
        if(smap[0][0] == '0') {
            dp[0][0] = 0;
            dfs(0, 0, res);
        }
        else {
            for(int num = 1; num <= 10; num++) {
                dp[0][0] = smap[0][0] == '+' ? num : -num;
                dfs(0, num, res);
            }
        }
    }    //    main-end
    
    private static String resString(int[] res) {
    	String ans = "[";
    	for(int i = 0; i < N; i++)
    		ans += (res[i] + " ");
    	ans += "]";
    	return ans;
    }
    
    private static void dfs(int nth, int num, int[] res) {
        if(find)    //    이미 smap을 만족하는 수열 찾은 경우
            return;
        
        res[nth] = num;
        
        if(nth == N - 1) {    //    수열 완성된 경우
            find = true;
            
            for(int i = 0; i < N; i++) {
                if(smap[i][i] == '-')
                    sb.append("-").append(res[i]).append(" ");
                else
                    sb.append(res[i]).append(" ");
            }
            
            System.out.println(sb);
            return;
        }
        
        if(smap[nth + 1][nth + 1] == '0') {    //    다음 수가 0일 경우
            dp[nth + 1][nth + 1] = 0;
            
            for(int row = nth; row >= 0; row--)
                dp[row][nth + 1] = dp[row][nth];    //    0은 합에 영향 미치지 않음
            
            dfs(nth + 1, 0, res);
        }
        else {
            for(int nnum = 1; nnum <= 10; nnum++) {
                dp[nth + 1][nth + 1] = smap[nth + 1][nth + 1] == '+' ? nnum : -nnum;
                boolean ok = true;
                
                for(int row = nth; row >= 0; row--) {
                    dp[row][nth + 1] = dp[row][nth] + dp[nth + 1][nth + 1];
                    
                    if(smap[row][nth + 1] == '0' && dp[row][nth + 1] != 0) {
                        ok = false;
                        break;
                    }
                    else if(smap[row][nth + 1] == '+' && dp[row][nth + 1] <= 0) {
                        ok = false;
                        break;
                    }
                    else if(smap[row][nth + 1] == '-' && dp[row][nth + 1] >= 0) {
                        ok = false;
                        break;
                    }
                }
                
                if(ok)
                    dfs(nth + 1, nnum, res);
            }
        }
    }
}    //    Main-class-end
```