# 26_02_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1234 크리스마스 트리

[1234번: 크리스마스 트리](http://boj.ma/1234/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;
public class Main {
  private static final long NONE = -1;
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer st;

  private static int N; 			// 트리 레벨
  private static int R;
  private static int G;
  private static int B;		        // 처음 주어진 빨강, 초록, 파랑 장식 개수
  private static long[][][][] dp;   // dp[i][j][k][l] : i층까지 빨간색 j개, 초록색 k개, 파란색 l개 사용하는 경우의 수

  private static int[] factorial;	//	factorial[i] : i!

  public static void main(String[] args) throws IOException {
    st = new StringTokenizer(br.readLine());
    N = Integer.parseInt(st.nextToken());
    R = Integer.parseInt(st.nextToken());
    G = Integer.parseInt(st.nextToken());
    B = Integer.parseInt(st.nextToken());

    calcFact();
    initTable();

    System.out.println(dfs(1, 0, 0, 0));
  } //  main-end

  private static void initTable() {
    dp = new long[11][101][101][101];	//	트리 최대 10층까지 존재, 장식 각각 최대 100개
    for(int i = 0; i < 11; i++) {
      for(int j = 0; j < 101; j++) {
        for(int k = 0; k < 101; k++)
          Arrays.fill(dp[i][j][k], NONE);
      }
    }
  }

  //	순열 경우의 수 계산
  private static void calcFact() {
    factorial = new int[N + 1];

    //	0! = 1! = 1
    factorial[0] = factorial[1] = 1;

    //	n! = n * (n - 1)!
    for(int i = 2; i <= N; i++)
      factorial[i] = i * factorial[i - 1];
  }

  //	n개 중 같은 것이 r개, g개, b개일 때 경우의 수 계산 
  private static int calcSamePerm(int n, int r, int g, int b) {
    return factorial[n] / (factorial[r] * factorial[g] * factorial[b]);
  }

  private static long dfs(int level, int r, int g, int b) {
    if(level > N)	//	N층까지 r개, g개, b개를 사용해 꾸미는 경우 존재하므로 1 반환
      return 1;

    if(dp[level][r][g][b] != NONE)	//	이미 계산된 경우
      return dp[level][r][g][b];

    dp[level][r][g][b] = 0;

    //	level 층을 한 가지 색으로만 꾸밀 수 있을 경우
    //	빨간색 장식 남은 개수(R - r)가 level보다 많거나 같을 경우 빨간색 장식만으로 꾸밀 수 있음
    if(R - r >= level)
      dp[level][r][g][b] += dfs(level + 1, r + level, g, b);
    //	초록색 장식 남은 개수(G - g)가 level보다 많거나 같을 경우 초록색 장식만으로 꾸밀 수 있음
    if(G - g >= level)
      dp[level][r][g][b] += dfs(level + 1, r, g + level, b);
    //	파란색 장식 남은 개수(B - b)가 level보다 많거나 같을 경우 파란색 장식만으로 꾸밀 수 있음
    if(B - b >= level)
      dp[level][r][g][b] += dfs(level + 1, r, g, b + level);

    //	level이 짝수일 경우 두 가지 색으로 골고루 꾸밀 수 있음
    if(level % 2 == 0) {
      //	빨간색 장식 남은 개수(R - r)와 초록색 장식 남은 개수(G - g)가 각각 level / 2보다 많거나 작을 경우 빨간색 장식과 초록색 장식만으로 꾸밀 수 있음
      if(R - r >= level / 2 && G - g >= level / 2)
        //	level 개 중 빨간색 level / 2개, 초록색 level / 2개이므로 같은 것이 있는 순열 경우의 수
        dp[level][r][g][b] += (calcSamePerm(level, level / 2, level / 2, 0) * dfs(level + 1, r + level / 2, g + level / 2, b));
      //	빨간색 장식 남은 개수(R - r)와 파란색 장식 남은 개수(B - b)가 각각 level / 2보다 많거나 작을 경우 빨간색 장식과 초록색 장식만으로 꾸밀 수 있음
      if(R - r >= level / 2 && B - b >= level / 2)
        //	level 개 중 빨간색 level / 2개, 파란색 level / 2개이므로 같은 것이 있는 순열 경우의 수
        dp[level][r][g][b] += (calcSamePerm(level, level / 2, 0, level / 2) * dfs(level + 1, r + level / 2, g, b + level / 2));
      //	초록색 장식 남은 개수(G - g)와 파란색 장식 남은 개수(B - b)가 각각 level / 2보다 많거나 작을 경우 빨간색 장식과 초록색 장식만으로 꾸밀 수 있음
      if(G - g >= level / 2 && B - b >= level / 2)
        //	level 개 중 초록색 level / 2개, 파란색 level / 2개이므로 같은 것이 있는 순열 경우의 수
        dp[level][r][g][b] += (calcSamePerm(level, 0, level / 2, level / 2) * dfs(level + 1, r, g + level / 2, b + level / 2));
    }

    //	level이 3의 배수일 경우 세 가지 색으로 골고루 꾸밀 수 있음
    if(level % 3 == 0) {
      //	빨간색 장식 남은 개수(R - r)이 level / 3보다 많거나 같을 경우
      //	초록색 장식 남은 개수(G - g)이 level / 3보다 많거나 같을 경우
      //	파란색 장식 남은 개수(B - b)이 level / 3보다 많거나 같을 경우
      //	위 세 가지 조건 모두 만족할 경우 꾸밀 수 있음
      if(R - r >= level / 3 && G - g >= level / 3 && B - b >= level / 3)
        //	level 개 중 빨간색 level / 3개, 초록색 level / 3개, 파란색 level / 3개이므로 같은 것이 있는 순열 경우의 수
        dp[level][r][g][b] += (calcSamePerm(level, level / 3, level / 3, level / 3) * dfs(level + 1, r + level / 3, g + level / 3, b + level / 3));
    }

    return dp[level][r][g][b];
  }
} //  Main-class-end
```