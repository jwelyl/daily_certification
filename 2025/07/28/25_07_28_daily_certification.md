# 25_07_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1687 행렬 찾기

[1687번: 행렬 찾기](http://boj.ma/1687/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
    
	private static int N;
	private static int M;
    
	private static int[][] prefixSum;
    
	private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        prefixSum = new int[N + 1][M + 1];
        
        for(int y = 1; y <= N; y++) {
            String input = br.readLine();
            for(int x = 1; x <= M; x++)
                prefixSum[y][x] = prefixSum[y][x - 1] + prefixSum[y - 1][x] - prefixSum[y - 1][x - 1] + input.charAt(x - 1) - '0';
        }
        
        for(int y1 = 1; y1 <= N; y1++) {
            for(int y2 = y1; y2 <= N; y2++) {
                //    행 범위가 [y1, y2]인 부분 행렬 중 0으로만 이루어진 것 찾기
                int x1 = 1;
                
                while(x1 <= M) {
                    int x2 = x1;
                    for(; x2 <= M; x2++) {
                        int ps = partialSum(y1, x1, y2, x2);
                        
                        if(ps == 0)    //    좌상단 (y1, x1), 우하단 (y2, x2)인 부분행렬이 0으로만 이루어져 있을 경우
                            answer = Math.max(answer, (y2 - y1 + 1) * (x2 - x1 + 1));
                        else
                            break;
                    }
                    
                    x1 = x2 + 1;
                }
            }
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static int partialSum(int y1, int x1, int y2, int x2) {
        return prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1];
    }
}    //    Main-class-end
```

### BOJ 14863 서울에서 경산까지

[14863번: 서울에서 경산까지](http://boj.ma/14863/t)

```java
import java.io.*;
import java.util.*;

public class Main {
    static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static StringTokenizer st;
    
    static int N;    //    구간 수
    static int K;    //    시간 제한
    
    static int[][] info;    //    info[i] : i번째 구간의 정보 {도보 이동 시 시간, 도보 이동 시 모금액, 자전거 이동 시 시간, 자전거 이동 시 모금액}
    static int[][] dp;      //    dp[i][j] : i번째 구간까지 j 시간 이내 통과했을때 최대 모금액
    
    static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        info = new int[N + 1][4];
        dp = new int[N + 1][K + 1];
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 0; j < 4; j++)
                info[i][j] = Integer.parseInt(st.nextToken());
        }
        
        dp[1][info[1][0]] = Math.max(dp[1][info[1][0]], info[1][1]);
        dp[1][info[1][2]] = Math.max(dp[1][info[1][2]], info[1][3]);
        
        for(int i = 2; i <= N; i++) {
        	for(int j = 1; j <= K; j++) {
        		if(dp[i - 1][j] != 0) {	//	i - 1번 구간까지 j 안에 올 수 있을 경우
        			if(j + info[i][0] <= K)	//	j 시간부터 걸어서 이동해도 K 안에 i 구간까지 올 수 있을 경우
        				dp[i][j + info[i][0]] = Math.max(dp[i][j + info[i][0]], dp[i - 1][j] + info[i][1]);
        			if(j + info[i][2] <= K)	//	j 시간부터 자전거로 이동해도 K 안에 i 구간까지 올 수 있을 경우
        				dp[i][j + info[i][2]] = Math.max(dp[i][j + info[i][2]], dp[i - 1][j] + info[i][3]);
        		}
        	}
        }
        
        for(int j = 1; j <= K; j++)
        	answer = Math.max(answer, dp[N][j]);
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```