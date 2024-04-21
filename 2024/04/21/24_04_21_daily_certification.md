# 24_04_21_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 정수 n개의 합 3**

[https://www.codetree.ai/missions/8/problems/sum-of-n-integers-3/introduction](https://www.codetree.ai/missions/8/problems/sum-of-n-integers-3/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-n-integers-3/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  정사각형 크기
    private static int k;   //  부분 정사각형 크기

    private static int[][] prefixSum;   //  정사각형 누적합

    private static int ans = -1;    //  크기 k * k인 부분 정사각형 합 최대

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        prefixSum = new int[n + 1][n + 1];

        for(int i = 1; i <= n; i++) {
            tokens = new StringTokenizer(br.readLine());
            for(int j = 1; j <= n; j++) {
                prefixSum[i][j] = prefixSum[i][j - 1] + prefixSum[i - 1][j] 
                                - prefixSum[i - 1][j - 1] + Integer.parseInt(tokens.nextToken());
            }
        }

        for(int i = 1; i <= n - k + 1; i++) {
            for(int j = 1; j <= n - k + 1; j++) {
                int sy = i;
                int sx = j;
                int ey = sy + k - 1;
                int ex = sx + k - 1;

                int partialSum = prefixSum[ey][ex] 
                            - prefixSum[sy - 1][ex] - prefixSum[ey][sx - 1] 
                            + prefixSum[sy - 1][sx - 1];

                ans = Math.max(ans, partialSum);
            }
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 부분 수열의 합이 k**

[https://www.codetree.ai/missions/8/problems/the-sum-of-the-subsequences-is-k/description](https://www.codetree.ai/missions/8/problems/the-sum-of-the-subsequences-is-k/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-sum-of-the-subsequences-is-k/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  정사각형 크기
    private static int k;   //  구간 합 k

    private static int[] prefixSum;   //  누적합

    private static int ans = 0;    //  부분합 k인 구간 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        prefixSum = new int[n + 1];

        tokens = new StringTokenizer(br.readLine());
        for(int i = 1; i <= n; i++) {
            prefixSum[i] = prefixSum[i - 1] + Integer.parseInt(tokens.nextToken());
        }

        int start = 1;
        int end = 1;

        while(end <= n) {
            int partialSum = partialSum(start, end);

            //  부분합이 모자란다면
            if(partialSum < k)
                end++;
            //  부분합이 일치한다면
            else if(partialSum == k) {
                ans++;
                start++;
                end++;
            }
            //  부분합이 초과한다면
            else {
                start++;
                if(start > end)
                    end = start;
            }
        }

        System.out.println(ans);
    }   //  main-end

    //  [from. to] 구간 합
    private static int partialSum(int from, int to) {
        return prefixSum[to] - prefixSum[from - 1];
    }
}   //  Main-class-end
```

**CO{)E TREE  연속한 K개의 숫자**

[https://www.codetree.ai/missions/8/problems/k-numbers-in-a-row/description](https://www.codetree.ai/missions/8/problems/k-numbers-in-a-row/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/k-numbers-in-a-row/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  1~n까지의 수
    private static int k;   //  연속한 k개의 수
    private static int b;   //  현재 없는 수 개수

    private static int[] nums;  //  nums[i] : 수 i가 있으면 1, 없으면 0

    private static int ans = Integer.MAX_VALUE;    //  연속한 k개의 수가 존재하기 위해 추가해야 할 최소 숫자 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());
        b = Integer.parseInt(tokens.nextToken());

        nums = new int[n + 1];
        Arrays.fill(nums, 1);

        for(int i = 1; i <= b; i++) {
            nums[Integer.parseInt(br.readLine())] = 0;
        }

        int start = 1;
        int end = 1;

        int kSum = 0;
        for(int i = 1; i <= k; i++)
            kSum += nums[i];

        ans = Math.min(ans, k - kSum);

        for(int i = 2; i <= n - k + 1; i++) {
            kSum -= nums[i - 1];
            kSum += nums[i + k - 1];
            ans = Math.min(ans, k - kSum);
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```