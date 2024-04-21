# 24_04_22_daily_certification

```
[koreii] #113 데일리인증
20240422
알고리즘 & 코딩 테스트 대비
- 코드트리 누적합 문제풀이
```

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 범위 내에 있는 점의 수 2**

[https://www.codetree.ai/missions/8/problems/the-number-of-points-within-the-range-2/description](https://www.codetree.ai/missions/8/problems/the-number-of-points-within-the-range-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-number-of-points-within-the-range-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MAX = 1_000_002;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  점의 개수
    private static int q;   //  범위 개수

    private static final boolean[] pos = new boolean[MAX];  //  pos[i] : 위치에 점이 있을 경우 true
    private static final int[] prefixCnt = new int[MAX];  //  prefixCnt[i] : 위치 1부터 i까지 누적 점의 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        q = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 1; i <= n; i++)
            pos[Integer.parseInt(tokens.nextToken()) + 1] = true;

        for(int i = 1; i < MAX; i++)
            prefixCnt[i] = prefixCnt[i - 1] + (pos[i] ? 1 : 0);

        for(int i = 0; i < q; i++) {
            tokens = new StringTokenizer(br.readLine());

            int from = Integer.parseInt(tokens.nextToken()) + 1;
            int to = Integer.parseInt(tokens.nextToken()) + 1;

            sb.append(prefixCnt[to] - prefixCnt[from - 1]).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 구간에 속한 문자의 개수**

[https://www.codetree.ai/missions/8/problems/the-number-of-characters-in-the-interval/description](https://www.codetree.ai/missions/8/problems/the-number-of-characters-in-the-interval/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-number-of-characters-in-the-interval/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  세로 길이
    private static int m;   //  가로 길이
    private static int k;   //  질의 개수

    private static int[][] aCnt; // aCnt[i][j] : (1, 1)부터 (i, j)까지 a의 개수
    private static int[][] bCnt; // bCnt[i][j] : (1, 1)부터 (i, j)까지 b의 개수
    private static int[][] cCnt; // cCnt[i][j] : (1, 1)부터 (i, j)까지 c의 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        aCnt = new int[n + 1][m + 1];
        bCnt = new int[n + 1][m + 1];
        cCnt = new int[n + 1][m + 1];

        for(int i = 0; i < n; i++) {
            String input = br.readLine();

            for(int j = 0; j < m; j++) {
                char ch = input.charAt(j);

                switch(ch) {
                case 'a':
                    aCnt[i + 1][j + 1] = aCnt[i + 1][j] + aCnt[i][j + 1] - aCnt[i][j] + 1;
                    bCnt[i + 1][j + 1] = bCnt[i + 1][j] + bCnt[i][j + 1] - bCnt[i][j];
                    cCnt[i + 1][j + 1] = cCnt[i + 1][j] + cCnt[i][j + 1] - cCnt[i][j];
                    break;
                case 'b':
                    aCnt[i + 1][j + 1] = aCnt[i + 1][j] + aCnt[i][j + 1] - aCnt[i][j];
                    bCnt[i + 1][j + 1] = bCnt[i + 1][j] + bCnt[i][j + 1] - bCnt[i][j] + 1;
                    cCnt[i + 1][j + 1] = cCnt[i + 1][j] + cCnt[i][j + 1] - cCnt[i][j];
                    break;
                case 'c':
                    aCnt[i + 1][j + 1] = aCnt[i + 1][j] + aCnt[i][j + 1] - aCnt[i][j];
                    bCnt[i + 1][j + 1] = bCnt[i + 1][j] + bCnt[i][j + 1] - bCnt[i][j];
                    cCnt[i + 1][j + 1] = cCnt[i + 1][j] + cCnt[i][j + 1] - cCnt[i][j] + 1;
                    break;
                }
            }
        }

        for(int i = 0; i < k; i++) {
            tokens = new StringTokenizer(br.readLine());

            int sy = Integer.parseInt(tokens.nextToken());
            int sx = Integer.parseInt(tokens.nextToken());
            int ey = Integer.parseInt(tokens.nextToken());
            int ex = Integer.parseInt(tokens.nextToken());

            sb.append(aCnt[ey][ex] - aCnt[sy - 1][ex] - aCnt[ey][sx - 1] + aCnt[sy - 1][sx - 1]).append(" ");
            sb.append(bCnt[ey][ex] - bCnt[sy - 1][ex] - bCnt[ey][sx - 1] + bCnt[sy - 1][sx - 1]).append(" ");
            sb.append(cCnt[ey][ex] - cCnt[sy - 1][ex] - cCnt[ey][sx - 1] + cCnt[sy - 1][sx - 1]).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```