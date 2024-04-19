# 24_04_19_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 연속 부분 합의 최댓값 구하기**

[https://www.codetree.ai/missions/2/problems/max-of-partial-sum/description](https://www.codetree.ai/missions/2/problems/max-of-partial-sum/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/max-of-partial-sum/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;
    private static int[] dp;
    private static int max = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        dp = new int[n];

        tokens = new StringTokenizer(br.readLine());
        dp[0] = Integer.parseInt(tokens.nextToken());
        max = Math.max(max,dp[0]);

        for(int i = 1; i < n; i++) {
            int num = Integer.parseInt(tokens.nextToken());

            dp[i] = Math.max(dp[i - 1] + num, num);
            max = Math.max(max, dp[i]);
        }

        System.out.println(max);
    }   //  main-end
}   //  Main-class-end
```