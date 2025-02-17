# 25_02_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3673 **나눌 수 있는 부분 수열**

[3673번: 나눌 수 있는 부분 수열](http://boj.ma/3673/t)

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
    private static int D;
    private static int N;
    
    private static int[] prefixSum;      //    prefixSum[nth] : arr[1..nth]까지의 누적합을 D로 나눈 나머지
    private static int[] cnt;            //    cnt[d] : prefixSum[nth]가 d인 nth의 개수
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            long answer = 0;
            
            st = new StringTokenizer(br.readLine());
            D = Integer.parseInt(st.nextToken());
            N = Integer.parseInt(st.nextToken());
            
            prefixSum = new int[N + 1];
            cnt = new int[D];
            cnt[0] = 1;
            
            st = new StringTokenizer(br.readLine());
            for(int i = 1; i <= N; i++) {
                prefixSum[i] = (prefixSum[i - 1] + Integer.parseInt(st.nextToken()) % D) % D;
                cnt[prefixSum[i]]++;
            }
            
            for(int d = 0; d < D; d++)
                answer += (long)cnt[d] * (cnt[d] - 1) / 2;
            
            sb.append(answer).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1509 팰린드롬 분할

[1509번: 팰린드롬 분할](http://boj.ma/1509/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static char[] text;
    private static boolean[][] isPalindrome;	//	isPalindrome[i][j] : text[i:j]가 palindrome이면 true
    private static int[] dp;					//	dp[i] : text[0:i]까지를 팰린드롬 부분 문자열로 분할할때 최소 분할 개수
    
    public static void main(String[] args) throws IOException {
    	text = br.readLine().toCharArray();
    	
    	isPalindrome = new boolean[text.length][text.length];
    	dp = new int[text.length];
    	
    	Arrays.fill(dp, text.length);
    
    	for(int k = 0; k < text.length; k++) {
    		for(int y = 0; y < text.length - k; y++) {
    			int x = y + k;
    			
    			if(y == x)
    				isPalindrome[y][x] = true;
    			else {
    				if(x == y + 1)
    					isPalindrome[y][x] = (text[y] == text[x]);
    				else
    					isPalindrome[y][x] = (text[y] == text[x]) && isPalindrome[y + 1][x - 1];
    			}
    		}
    	}

    	for(int i = 0; i < text.length; i++) {
    		for(int j = i; j >= 0; j--) {
    			if(isPalindrome[j][i])	//	text[j:i]가 palindrome일 경우
    				dp[i] = Math.min(dp[i], j - 1 >= 0 ? dp[j - 1] + 1 : 1);
    		}
    	}
    	
    	System.out.println(dp[text.length - 1]);
    }    //    main-end
}    //    Main-class-end
```