# 25_12_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2612 DNA 유사도

[2612번: DNA 유사도](http://boj.ma/2612/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int len1 = Integer.parseInt(br.readLine());
    char[] dna1 = br.readLine().toCharArray();
    int len2 = Integer.parseInt(br.readLine());
    char[] dna2 = br.readLine().toCharArray();

    int answer = Integer.MIN_VALUE;
    int end1 = 0;
    int end2 = 0;
    int cur1 = 0;
    int cur2 = 0;

    int[][] dp = new int[len1 + 1][len2 + 1];
    for(int i = 1; i <= len1; i++) {
      for(int j = 1; j <= len2; j++) {
        //  dna1의 i번째 문자와 dna2의 j번째 문자가 같을 경우, 3점 추가하는게 이득임
        if(dna1[i - 1] == dna2[j - 1])
          dp[i][j] = dp[i - 1][j - 1] + 3;
        //  dna1의 i번째 문자와 dna2의 j번째 문자가 다를 경우
        else
          dp[i][j] = Math.max(Math.max(dp[i - 1][j - 1], Math.max(dp[i - 1][j], dp[i][j - 1])) - 2, 0);
      }
    }

    for(int i = 1; i <= len1; i++) {
      for(int j =1 ; j <= len2; j++) {
        if(dp[i][j] > answer) {
          answer = dp[i][j];
          end1 = i;
          end2 = j;
        }
      }
    }

    cur1 = end1;
    cur2 = end2;

    while(dp[cur1][cur2] != 0) {
      //  cur1이 1 증가하면서 불일치가 발생해 -2점 된 경우
      if(dp[cur1 - 1][cur2] == dp[cur1][cur2] + 2)
        cur1--;
      //  cur2가 1 증가하면서 불일치가 발생해 -2점 된 경우
      else if(dp[cur1][cur2 - 1] == dp[cur1][cur2] + 2)
        cur2--;
      //  cur1, cur2 1 증가하면서 일치가 발생해 +3점 된 경우
      else {
        cur1--;
        cur2--;
      }
    }

    sb.append(answer).append("\n");
    for(int i = cur1 + 1; i <= end1; i++)
      sb.append(dna1[i - 1]);
    sb.append("\n");
    for(int i = cur2 + 1; i <= end2; i++)
      sb.append(dna2[i - 1]);
    System.out.println(sb);
  }    //    main-end
}    //    Main-class-end
```