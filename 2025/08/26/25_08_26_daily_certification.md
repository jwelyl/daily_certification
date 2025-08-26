# 25_08_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2579 계단 오르기

[2579번: 계단 오르기](http://boj.ma/2579/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;            //    계단 개수
    private static int[] steps;      //    steps[i] : i번째 계단을 밟았을때 얻을 수 있는 점수
    
    //    dp[i][0] : i - 1번째 계단을 밟지 않고 i번째 계단을 밟았을 때 얻을 수 있는 최대 점수
    //    dp[i][1] : i - 1번째 계단을 밟고 i번째 계단을 밟았을 때 얻을 수 있는 최대 점수
    private static int[][] dp;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        steps = new int[N + 1];
        dp = new int[N + 1][2];
        
        for(int i = 1; i <= N; i++)
            steps[i] = Integer.parseInt(br.readLine());
        
        dp[1][0] = steps[1];
        dp[1][1] = steps[1];
        
        if(N >= 2) {
            dp[2][0] = steps[2];                //    첫번째 계단을 밟지 않고 두번째 계단만 밟음
            dp[2][1] = steps[1] + steps[2];     //    첫번째 계단, 두번째 계단 둘다 밟음
        }
        
        if(N >= 3) {
            for(int i = 3; i <= N; i++) {
                //    i - 1번째 계단을 밟지 않을 경우, 
                dp[i][0] = Math.max(dp[i - 2][0], dp[i - 2][1]) + steps[i];
                //    i - 1번째 계단을 밟을 경우, i - 2번째 계단은 밟을 수 없음
                dp[i][1] = dp[i - 1][0] + steps[i];
            }
        }
        
        System.out.println(Math.max(dp[N][0], dp[N][1]));
    }    //    main-end
}    //    Main-class-end
```

### BOJ 2156 포도주 시식

[2156번: 포도주 시식](http://boj.ma/2156/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;              //    포도주 잔 개수
    private static int[] glasses;      //    glasses[i] : i번째 포도주 잔에 들어있는 포도주 양
    
    //    dp[i][0] : i - 1번째 포도주 잔의 포도주를 마시지 않고 i번째 포도주를 마셨을때 마실 수 있는 최대 포도주 양
    //    dp[i][1] : i - 1번째 포도주 잔의 포도주를 마시고 i번째 포도주를 마셨을때 마실 수 있는 최대 포도주 양
    private static int[][] dp;
    
    private static int answer = 0;    //    최대로 마실 수 있는 포도주 양
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        glasses = new int[N + 1];
        dp = new int[N + 1][2];
        
        for(int i = 1; i <= N; i++)
            glasses[i] = Integer.parseInt(br.readLine());
        
        dp[1][0] = glasses[1];
        dp[1][1] = glasses[1];
        answer = glasses[1];
        
        if(N >= 2) {
            dp[2][0] = glasses[2];                  //    첫번째 포도주 잔의 포도주를 마시지 않고 두번째 포도주 잔의 포도주만 마심
            dp[2][1] = glasses[1] + glasses[2];     //    첫번째 포도주 잔의 포도주를 마시고 두번째 포도주 잔의 포도주도 마심
            answer = glasses[1] + glasses[2];
        }
        
        if(N >= 3) {
            for(int i = 3; i <= N; i++) {
                //    i - 1번째 포도주 잔의 포도주를 마시지 않은 경우
                //    i - 2번째 포도주 잔까지의 최댓값
                //    i - 3번째 포도주 잔까지의 최댓값
                //    i - 4번째 포도주 잔까지의 최댓값 x (i - 3, i - 2, i - 1 중 i - 2는 마시는게 무조건 이득임)
                dp[i][0] = Math.max(Math.max(dp[i - 3][0], dp[i - 3][1]), Math.max(dp[i - 2][0], dp[i - 2][1])) + glasses[i];
                //    i - 1번째 포도주 잔의 포도주를 마신 경우, i - 2번째 포도주 잔의 포도주는 마실 수 없음
                //    dp[i - 1][0] : i - 2번째 포도주 잔의 포도주를 마시지 않았을떄 최댓값
                dp[i][1] = dp[i - 1][0] + glasses[i];
            
                answer = Math.max(answer, Math.max(dp[i][0], dp[i][1]));
            }
        }
        /*
        System.out.println("dp[0] : ");
        for(int i = 1; i <= N; i++)
            System.out.print(dp[i][0] + " ");
        System.out.println();
        System.out.println("dp[1] : ");
        for(int i = 1; i <= N; i++)
            System.out.print(dp[i][1] + " ");
        System.out.println();
        */
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 9465 스티커

[9465번: 스티커](http://boj.ma/9465/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;              //    테스트케이스 개수
    private static int N;              //    스티커 개수 2 * N
    private static int[][] stickers;   //    stickers[i][j] : i행 j열의 스티커 점수
     
    private static int[][] dp;         //    dp[i][j] : i행 j열의 스티커를 반드시 사용한다고 가정했을때 j열까지 사용해서 얻을 수 있는 최대 점수
    
    private static int answer = 0;     //    스티커 사용해서 얻을 수 있는 최대 점수
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine());
        
            stickers = new int[2][N + 1];
            dp = new int[2][N + 1];
            answer = 0;
        
            for(int i = 0; i < 2; i++) {
                st = new StringTokenizer(br.readLine());
                for(int j = 1; j <= N; j++)
                    stickers[i][j] = Integer.parseInt(st.nextToken());
            }
        
            dp[0][1] = stickers[0][1];
            dp[1][1] = stickers[1][1];
            answer = Math.max(dp[0][1], dp[1][1]);
        
            if(N >= 2) {
                dp[0][2] = stickers[1][1] + stickers[0][2];
                dp[1][2] = stickers[0][1] + stickers[1][2];
                answer = Math.max(answer, Math.max(dp[0][2], dp[1][2]));
            }
        
            if(N >= 3) {
                for(int i = 3; i <= N; i++) {
                    //    i번째 열의 위쪽 스티커를 사용하려면
                    //    i - 1번째 열의 아래쪽 스티커를 사용하고 사용하던가
                    //    i - 2번째 열의 아무 스티커나 사용하고 사용하기
                    dp[0][i] = Math.max(dp[1][i - 1], Math.max(dp[0][i - 2], dp[1][i - 2])) + stickers[0][i];
                    //    i번째 열의 아래쪽 스티커를 사용하려면
                    //    i - 1번째 열의 위쪽 스티커를 사용하고 사용하던가
                    //    i - 2번째 열의 아무 스티커나 사용하고 사용하기
                    dp[1][i] = Math.max(dp[0][i - 1], Math.max(dp[0][i - 2], dp[1][i - 2])) + stickers[1][i];
                
                    answer = Math.max(answer, Math.max(dp[0][i], dp[1][i]));
                }
            }

            sb.append(answer).append("\n");
        }    
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```