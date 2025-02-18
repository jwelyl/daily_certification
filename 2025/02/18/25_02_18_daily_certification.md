# 25_02_18_daily_certification

```
[#049 koreii] 데일리인증 20250218
1. 코딩 테스트 대비 알고리즘 학습
- DP, Number Theory (BOJ 20500)
- DP (BOJ 2602 돌다리 건너기)
2. 자격증
- ADsp 벼락치기 (요약식)
```

# Problem Solving (Algorithm & SQL)

### BOJ 20500 **Ezreal 여눈부터 가네 ㅈㅈ**

[20500번: Ezreal 여눈부터 가네 ㅈㅈ](http://boj.ma/20500/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MOD = 1_000_000_007;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    private static int[][] dp;	//	dp[len][r] : len 자릿수를 1, 5로만 구성했을때 해당 수를 3으로 나눈 나머지가 r인 경우의 수
    
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	
    	if(N == 1) {
    		System.out.println(0);
    		return;
    	}
    	
    	dp = new int[N][3];
    	
    	dp[1][0] = 0;
    	dp[1][1] = 1;
    	dp[1][2] = 1;
    	
    	for(int len = 2; len < N; len++) {
    		dp[len][0] = (dp[len - 1][1] % MOD + dp[len - 1][2] % MOD) % MOD;
    		dp[len][1] = (dp[len - 1][0] % MOD + dp[len - 1][2] % MOD) % MOD;
    		dp[len][2] = (dp[len - 1][0] % MOD + dp[len - 1][1] % MOD) % MOD;
    	}
    	
    	System.out.println(dp[N - 1][1]);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 2602 돌다리 건너기

[2602번: 돌다리 건너기](http://boj.ma/2602/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static char[] scroll;
	private static char[][] bridge;

	private static int N; // 두루마리 길이
	private static int M; // 돌다리 길이

	// dp[0][i][j] : bridge[0]의 i번째 문자에 j번째로 도달할 수 있는 경우의 수
	// dp[1][i][j] : bridge[1]의 i번째 문자에 j번째로 도달할 수 있는 경우의 수
	private static int[][][] dp;

	private static int answer = 0;

	public static void main(String[] args) throws IOException {
		scroll = br.readLine().toCharArray();
		N = scroll.length;

		bridge = new char[2][];

		bridge[0] = br.readLine().toCharArray();
		bridge[1] = br.readLine().toCharArray();
		M = bridge[0].length;

		dp = new int[2][M][N];

		for (int i = 0; i < 2; i++) { // i번째 다리
			for (int j = 0; j < M; j++) {
				if (bridge[i][j] == scroll[0])
					dp[i][j][0]++;
			}
		}

		for (int j = 1; j < N; j++) { // j번째 도착하는 것
			for (int i = 0; i < 2; i++) {
				for (int k = 0; k < M; k++) {
					if (bridge[i][k] == scroll[j]) { // i번째 다리의 k번째 문자가 두루마리의 j번째 문자와 같다면
						for (int l = 0; l < k; l++) {
							if (bridge[1 - i][l] == scroll[j - 1])
								dp[i][k][j] += dp[1 - i][l][j - 1];
						}
					}
				}
			}
		}

		for (int i = 0; i < M; i++)
			answer += (dp[0][i][N - 1] + dp[1][i][N - 1]);

		System.out.println(answer);
	} // main-end
} // Main-class-end
```