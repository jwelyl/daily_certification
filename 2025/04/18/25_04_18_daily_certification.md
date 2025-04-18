# 25_04_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2169 로봇 조종하기

[2169번: 로봇 조종하기](http://boj.ma/2169/t)

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
    
    private static int[][] map;
    private static int[][] dp;    //    dp[y][x] : (0, 0)부터 (y, x)까지 가면서 탐사한 지역들의 가치 합의 최댓값
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new int[N][M];
        dp = new int[N][M];
        
        for(int y = 0; y < N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 0; x < M; x++)
                map[y][x] = Integer.parseInt(st.nextToken());
        }
        
        dp[0][0] = map[0][0];
        
        //	0행은 (0, 0)에서 직진해서 이동할 수 밖에 없음 (아래로 가면 다시 못 올라옴)
        for(int x = 1; x < M; x++)
        	dp[0][x] = dp[0][x - 1] + map[0][x];
        
        for(int y = 1; y < N; y++) {
        	int[] lr = new int[M];	//	left -> right로 이동만 고려
        	int[] rl = new int[M];	//	right -> left로 이동만 고려
        	
        	lr[0] = dp[y - 1][0] + map[y][0];
        	for(int x = 1; x < M; x++)	// (y, x)까지 (y, x - 1)에서 이동하는 것과 (y - 1, x)에서 이동하는 것을 비교
        		lr[x] = Math.max(lr[x - 1], dp[y - 1][x]) + map[y][x];
        	
        	rl[M - 1] = dp[y - 1][M - 1] + map[y][M - 1];
        	for(int x = M - 2; x >= 0; x--)	//	(y, x)까지 (y, x + 1)에서 이동하는 것과 (y - 1, x)에서 이동하는 것을 비교
        		rl[x] = Math.max(rl[x + 1], dp[y - 1][x]) + map[y][x];
        	
        	for(int x = 0; x < M; x++)
        		dp[y][x] = Math.max(lr[x], rl[x]);
        }
        
        System.out.println(dp[N - 1][M - 1]);
    }    //    main-end
}    //    Main-class-end
```