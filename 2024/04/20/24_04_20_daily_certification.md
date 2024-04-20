# 24_04_20_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 사각형 채우기**

[https://www.codetree.ai/missions/2/problems/max-of-partial-sum/description](https://www.codetree.ai/missions/2/problems/max-of-partial-sum/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/max-of-partial-sum/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MOD = 10_007;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    private static int n;
    private static int[] dp;
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        dp = new int[n + 1];

        dp[1] = 1;

        if(n >= 2)
            dp[2] = 2;

        for(int i = 3; i <= n; i++)
            dp[i] = (dp[i - 1] % MOD + dp[i - 2] % MOD) % MOD;

        System.out.println(dp[n]);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 사각형 채우기 3**

[https://www.codetree.ai/missions/2/problems/climbing-stairs-2/description](https://www.codetree.ai/missions/2/problems/climbing-stairs-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/climbing-stairs-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MOD = 1_000_000_007;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    private static int n;
    private static long[] dp;
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        dp = new long[n + 1];

        dp[1] = 2;

        if(n >= 2)
            dp[2] = 7;

        dp[0] = 1;
        dp[1] = 2;

        for(int i = 2; i <= n; i++) {
            dp[i] = (dp[i - 1] * 2 + dp[i - 2] * 3) % MOD;
            for(int j = i - 3; j >= 0; j--)
                dp[i] = (dp[i] + dp[j] * 2) % MOD;
        }

        System.out.println(dp[n]);
    }   //  main-end
}   //  Main-class-end
```