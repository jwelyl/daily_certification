# 26_04_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2662 기업투자

[2662번: 기업투자](http://boj.ma/2662/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());
    
    int n = Integer.parseInt(st.nextToken());     //	투자 금액
    int m = Integer.parseInt(st.nextToken());     //	투자 가능한 기업 개수

    int[][] profits = new int[m + 1][n + 1];      //	profits[i][j] : i 기업에 j원 투자했을때 이익
    int[][] dp = new int[m + 1][n + 1];           //	dp[i][j] : 1~i 기업에 총 j원을 투자했을때 얻을 수
    int[][] investments = new int[m + 1][n + 1];  //	investments[i][j] : 1~i 기업에 총 j원을 투자해서 최대 이익을 얻었을때 j 기업에 투자한 금액

    int y;
    int x;

    Deque<Integer> stack = new ArrayDeque<>();
    for(int i = 1; i <= n; i++) {
      st = new StringTokenizer(br.readLine());
      st.nextToken();

      for(int j = 1; j <= m; j++)
        profits[j][i] = Integer.parseInt(st.nextToken());
    }

    for(int i = 1; i <= m; i++) {
      for(int j = 1; j <= n; j++) {
        for(int k = 0; k <= j; k++) {
          int profit = Math.max(dp[i][j], dp[i - 1][j - k] + profits[i][k]);	//	i 기업에 k원을 투자했을때 얻을 수 있는 이익

          //	i 기업에 k원을 투자하는게 최대일 경우
          if(dp[i][j] < profit) {
            dp[i][j] = profit;
            investments[i][j] = k;
          }
        }
      }
    }

    sb.append(dp[m][n]).append("\n");

    y = m;
    x = n;

    while(y > 0) {
      int investment = investments[y][x];
      stack.offerLast(investment);	//	총 x원 투자했을때 y 기업에 investment만큼 투자하는게 최대
      y--;
      x -= investment;	//	1~(y-1) 기업에 x - investment만큼 투자하는게 최대
    }

    while(!stack.isEmpty())
      sb.append(stack.pollLast()).append(" ");

    System.out.println(sb);
  } //	main-end
} //	Main-class-end
```