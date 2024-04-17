# 24_04_18_daily_certification

# 데이터베이스

## Normalization

### Anomalies

**Insertion Anomalies**

**Deletion Anomalies**

**Update Anomalies**

### Spurious Tuples

### NULL

### Functional Dependency

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 괄호 쌍 만들어주기**

[https://www.codetree.ai/missions/8/problems/pair-parentheses/description](https://www.codetree.ai/missions/8/problems/pair-parentheses/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/pair-parentheses/description)

**코드**

```java
import java.io.*;
import java.util.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    private static char[] parentheses;

    private static int[] right;

    private static long ans = 0;

    public static void main(String[] args) throws IOException {
        parentheses = br.readLine().toCharArray();

        int len = parentheses.length;

        right = new int[len];

        right[len - 1] = 0;
        for(int i = len - 2; i >= 0; i--) {
            if(parentheses[i] == ')' && parentheses[i + 1] == ')')
                right[i] = right[i + 1] + 1;
            else
                right[i] = right[i + 1];
        }

        for(int i = 1; i < len; i++) {
            if(parentheses[i - 1] == '(' && parentheses[i] == '(')
                ans += right[i];
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 이상한 폭탄**

[https://www.codetree.ai/missions/8/problems/strange-bomb/description](https://www.codetree.ai/missions/8/problems/strange-bomb/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/strange-bomb/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MAX = 1_000_001;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;
    private static int k;

    private static int[] bombs; //  폭탄 번호
    private static int[] cnt;   //  cnt[i] : [x, x + k]인 구간에서 번호가 i인 폭탄 등장 횟수

    private static int ans = -1;    //  어떤 [x, x + k] 구간에서 2번 이상 등장한 폭탄 중 가장 번호가 큰 폭탄 번호
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        bombs = new int[n];
        cnt = new int[MAX];

        for(int i = 0; i < n; i++)
            bombs[i] = Integer.parseInt(br.readLine());

        for(int i = 0; i <= k; i++) {
            if(i < n) {
                cnt[bombs[i]]++;    //  i번째 폭탄에 해당하는 번호 등장 횟수 증가

                if(cnt[bombs[i]] >= 2) {    //  구간 내에 2번 이상 등장했을 경우
                    ans = Math.max(ans, bombs[i]);
                }
            }
        }

        for(int i = 1; i <= n - 1 - k; i++) {
            cnt[bombs[i - 1]]--;    //  i-1번째 폭탄에 해당하는 번호 등장 횟수 1 감소
            cnt[bombs[i + k]]++;    //  i+1번째 폭탄에 해당하는 번호 등장 횟수 1 증가

            if(cnt[bombs[i + k]] >= 2) {    //  구간 내에 2번 이상 등장했을 경우
                ans = Math.max(ans, bombs[i + k]);
            }
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```