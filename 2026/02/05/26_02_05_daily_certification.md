# 26_02_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14863 서울에서 경산까지

[14863번: 서울에서 경산까지](http://boj.ma/14863/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //    구간 수
    int k = Integer.parseInt(st.nextToken()); //    시간 제한

    //    info[i] : i번째 구간의 정보 {도보 이동 시 시간, 도보 이동 시 모금액, 자전거 이동 시 시간, 자전거 이동 시 모금액}
    int[][] info = new int[n + 1][4];
    //    dp[i][j] : i번째 구간까지 j 시간 이내 통과했을때 최대 모금액
    int[][] dp = new int[n + 1][k + 1];

    int answer = 0;

    for(int i = 1; i <= n; i++) {
      st = new StringTokenizer(br.readLine());
      for(int j = 0; j < 4; j++)
        info[i][j] = Integer.parseInt(st.nextToken());
    }

    dp[1][info[1][0]] = Math.max(dp[1][info[1][0]], info[1][1]);
    dp[1][info[1][2]] = Math.max(dp[1][info[1][2]], info[1][3]);

    for(int i = 2; i <= n; i++) {
      for(int j = 1; j <= k; j++) {
        if(dp[i - 1][j] != 0) {	//	i - 1번 구간까지 j 안에 올 수 있을 경우
          if(j + info[i][0] <= k)	//	j 시간부터 걸어서 이동해도 K 안에 i 구간까지 올 수 있을 경우
            dp[i][j + info[i][0]] = Math.max(dp[i][j + info[i][0]], dp[i - 1][j] + info[i][1]);
          if(j + info[i][2] <= k)	//	j 시간부터 자전거로 이동해도 K 안에 i 구간까지 올 수 있을 경우
            dp[i][j + info[i][2]] = Math.max(dp[i][j + info[i][2]], dp[i - 1][j] + info[i][3]);
        }
      }
    }

    for(int j = 1; j <= k; j++)
      answer = Math.max(answer, dp[n][j]);
    System.out.println(answer);
  }    //    main-end
}    //    Main-class-end
```