# 24_10_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11066 파일 합치기

[](https://www.acmicpc.net/problem/11066)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스 개수
    private static int K;    //    파일 개수
    
    private static int[] files;    	//    파일 장수
    private static int[] prefixSum;	//	  누적합
    private static long[][] dp;    	//    dp[i][j] : i번째 파일부터 j번째 파일까지 합치는데 필요한 최소 비용
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            K = Integer.parseInt(br.readLine());
            files = new int[K + 1];
            prefixSum = new int[K + 1];
            dp = new long[K + 1][K + 1];
            
            st = new StringTokenizer(br.readLine());
            for(int i = 1; i <= K; i++) {
                Arrays.fill(dp[i], Long.MAX_VALUE);
                files[i] = Integer.parseInt(st.nextToken());
                prefixSum[i] = prefixSum[i - 1] + files[i];
                dp[i][i] = 0;
            }
            
            for(int d = 1; d < K; d++) {
                for(int y = 1; y <= K - d; y++) {
                    int x = y + d;
                    
                    for(int k = y; k < x; k++)
                    	dp[y][x] = Math.min(dp[y][x], (dp[y][k] + dp[k + 1][x]) + merge(y, x));
                }
            }
            
            sb.append(dp[1][K]).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static int merge(int from, int to) {
    	return prefixSum[to] - prefixSum[from - 1];
    }
}    //    Main-class-end
```

### BOJ 1007 벡터 매칭

[](https://www.acmicpc.net/problem/1007)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스 개수
    private static int N;    //    점 개수 (2 <= N <= 20, 짝수)
    
    private static int[] xpos;
    private static int[] ypos;
    
    private static int xSum = 0;    //    x 좌표 합
    private static int ySum = 0;    //    y 좌표 합
    
    private static double minSum = Double.MAX_VALUE;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine());
            
            xpos = new int[N];
            ypos = new int[N];
            minSum = Double.MAX_VALUE;
            
            xSum = 0;
            ySum = 0;
            
            for(int i = 0; i < N; i++) {
                st = new StringTokenizer(br.readLine());
                xpos[i] = Integer.parseInt(st.nextToken());
                ypos[i] = Integer.parseInt(st.nextToken());
                
                xSum += xpos[i];
                ySum += ypos[i];
            }
            
            combination(0, 0, new int[N / 2]);
            
            sb.append(minSum).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    //    selected : N개의 점 중 뺄 N / 2개의 점
    private static void combination(int nth, int start, int[] selected) {
        if(nth == N / 2) {
            int resXpos = xSum;
            int resYpos = ySum;    //    벡터 합의 (x, y) 좌표
            
            for(int i = 0; i < N / 2; i++) {
                resXpos -= 2 * xpos[selected[i]];
                resYpos -= 2 * ypos[selected[i]];
            }
            
            minSum = Math.min(minSum, Math.sqrt((long)resXpos * resXpos + (long)resYpos * resYpos));
            
            return;
        }
        
        for(int i = start; i < N; i++) {
            selected[nth] = i;
            combination(nth + 1, i + 1, selected);
        }
    }
}    //    Main-class-end
```