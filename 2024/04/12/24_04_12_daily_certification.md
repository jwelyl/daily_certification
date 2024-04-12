# 24_04_12_daily_certification

# 데이터베이스

## Transaction

### write-lock

### read-lock

### Two-Phase Locking Protocol

# Problem Solving (Algorithm & SQL)

**BOJ 1451 직사각형으로 나누기**

[1451번: 직사각형으로 나누기](https://www.acmicpc.net/problem/1451)

주어진 직사각형을 세 개의 직사각형으로 나누는 문제이다. 세 개의 직사각형을 정하면 세 직사각형의 합은 2차원 누적합과 부분합을 이용해 쉽게 구할 수 있다.

**n=1일 경우, 다음과 같이 나누는 경우밖에 없다.**

![n1.jpeg](24_04_12_daily_certification%20747686e7c3b94eaf88a2f4a609676126/n1.jpeg)

**관련 코드는 다음과 같다.**

```java
if(n == 1) {    //    세로로 한 줄일 경우
    for(int x1 = 1; x1 <= m - 2; x1++) {
      for(int x2 = x1 + 1; x2 <= m - 1; x2++) {
        int ps1 = partialSum(1, 1, 1, x1);
        int ps2 = partialSum(1, x1 + 1, 1, x2);
        int ps3 = partialSum(1, x2 + 1, 1, m);

        ans = Math.max(ans, (long)ps1 * ps2 *ps3);
      }
    }
  }
```

**m=1일 경우, 다음과 같이 나누는 경우밖에 없다.**

![m1.jpeg](24_04_12_daily_certification%20747686e7c3b94eaf88a2f4a609676126/m1.jpeg)

**관련 코드는 다음과 같다.**

```java
else if(m == 1) {    //    가로로 한 줄일 경우
  for(int y1 = 1; y1 <= n - 2; y1++) {
    for(int y2 = y1 + 1; y2 <= n - 1; y2++) {
      int ps1 = partialSum(1, 1, y1, 1);
      int ps2 = partialSum(y1 + 1, 1, y2, 1);
      int ps3 = partialSum(y2 + 1, 1, n, 1);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);
    }
  }
}
```

**n, m 둘 다 1이 아닐 경우는 다음과 같이 여러 경우가 존재한다.**

**먼저 좌상단이 (1, 1)인 직사각형의 우하단이 (y, x)이고 y < n, x < m인 경우이다.**

![nm1.jpeg](24_04_12_daily_certification%20747686e7c3b94eaf88a2f4a609676126/nm1.jpeg)

**관련 코드는 다음과 같다.**

```java
for(int y = 1; y <= n - 1; y++) {
    for(int x = 1; x <= m - 1; x++) {
      int ps1 = partialSum(1, 1, y, x);
      int ps2 = partialSum(1, x + 1, y, m);
      int ps3 = partialSum(y + 1, 1, n, m);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);

      ps2 = partialSum(y + 1, 1, n, x);
      ps3 = partialSum(1, x + 1, n, m);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);
    }
  }
```

**좌상단이 (1, 1)인 직사각형의 우하단이 (n, x1)인 경우이다.**

**나머지 부분을 두 개의 직사각형의 세로로 나눌 경우와 가로로 나눌 경우로 나뉜다.**

![nm2.jpeg](24_04_12_daily_certification%20747686e7c3b94eaf88a2f4a609676126/nm2.jpeg)

**관련 코드는 다음과 같다.**

```java
for(int y1 = 1; y1 < n; y1++) {
    for(int x1 = 1; x1 < m; x1++) {
      int ps1 = partialSum(1, 1, n, x1);
      int ps2 = partialSum(1, x1 + 1, y1, m);
      int ps3 = partialSum(y1 + 1, x1 + 1, n, m);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);
    }
  }

  for(int x1 = 1; x1 < m - 1; x1++) {
    for(int x2 = x1 + 1; x2 < m; x2++) {
      int ps1 = partialSum(1, 1, n, x1);
      int ps2 = partialSum(1, x1 + 1, n, x2);
      int ps3 = partialSum(1, x2 + 1, n, m);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);
    }
  }
```

**좌상단이 (1, 1)인 직사각형의 우하단이 (y1, m)인 경우이다.**

**나머지 부분을 두 개의 직사각형의 세로로 나눌 경우와 가로로 나눌 경우로 나뉜다.**

![nm3.jpeg](24_04_12_daily_certification%20747686e7c3b94eaf88a2f4a609676126/nm3.jpeg)

**관련 코드는 다음과 같다.**

```java
for(int y1 = 1; y1 < n - 1; y1++) {
    for(int y2 = y1 + 1; y2 < n; y2++) {
      int ps1 = partialSum(1, 1, y1, m);
      int ps2 = partialSum(y1 + 1, 1, y2, m);
      int ps3 = partialSum(y2 + 1, 1, n, m);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);
    }
  }

  for(int y1 = 1; y1 < n; y1++) {
    for(int x1 = 1; x1 < m; x1++) {
      int ps1 = partialSum(1, 1, y1, m);
      int ps2 = partialSum(y1 + 1, 1, n, x1);
      int ps3 = partialSum(y1 + 1, x1 + 1, n, m);

      ans = Math.max(ans, (long)ps1 * ps2 *ps3);
    }
  }
```

전체 코드는 다음과 같다.

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;

  private static int n;    //    직사각형 세로 길이
  private static int m;    //    직사각형 가로 길이

  private static int[][] prefSum;    //    prefSum[y][x] = 좌상단 (1, 1), 우하단 (y, x)인 직사각형 누적합

  private static long ans = 0L;

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    prefSum = new int[n + 1][m + 1];

    //    누적합 구하기
    for(int i = 1; i <= n; i++) {
      char[] input = br.readLine().toCharArray();
      for(int j = 1; j <= m; j++) {
        prefSum[i][j] = prefSum[i - 1][j] + prefSum[i][j - 1] + (input[j - 1] - '0') - prefSum[i - 1][j - 1];
      }
    }

    if(n == 1) {    //    세로로 한 줄일 경우
      for(int x1 = 1; x1 <= m - 2; x1++) {
        for(int x2 = x1 + 1; x2 <= m - 1; x2++) {
          int ps1 = partialSum(1, 1, 1, x1);
          int ps2 = partialSum(1, x1 + 1, 1, x2);
          int ps3 = partialSum(1, x2 + 1, 1, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }
    }
    else if(m == 1) {    //    가로로 한 줄일 경우
      for(int y1 = 1; y1 <= n - 2; y1++) {
        for(int y2 = y1 + 1; y2 <= n - 1; y2++) {
          int ps1 = partialSum(1, 1, y1, 1);
          int ps2 = partialSum(y1 + 1, 1, y2, 1);
          int ps3 = partialSum(y2 + 1, 1, n, 1);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }
    }
    else {
      for(int y = 1; y <= n - 1; y++) {
        for(int x = 1; x <= m - 1; x++) {
          int ps1 = partialSum(1, 1, y, x);
          int ps2 = partialSum(1, x + 1, y, m);
          int ps3 = partialSum(y + 1, 1, n, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);

          ps2 = partialSum(y + 1, 1, n, x);
          ps3 = partialSum(1, x + 1, n, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }

      for(int y1 = 1; y1 < n; y1++) {
        for(int x1 = 1; x1 < m; x1++) {
          int ps1 = partialSum(1, 1, n, x1);
          int ps2 = partialSum(1, x1 + 1, y1, m);
          int ps3 = partialSum(y1 + 1, x1 + 1, n, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }

      for(int x1 = 1; x1 < m - 1; x1++) {
        for(int x2 = x1 + 1; x2 < m; x2++) {
          int ps1 = partialSum(1, 1, n, x1);
          int ps2 = partialSum(1, x1 + 1, n, x2);
          int ps3 = partialSum(1, x2 + 1, n, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }

      for(int y1 = 1; y1 < n - 1; y1++) {
        for(int y2 = y1 + 1; y2 < n; y2++) {
          int ps1 = partialSum(1, 1, y1, m);
          int ps2 = partialSum(y1 + 1, 1, y2, m);
          int ps3 = partialSum(y2 + 1, 1, n, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }

      for(int y1 = 1; y1 < n; y1++) {
        for(int x1 = 1; x1 < m; x1++) {
          int ps1 = partialSum(1, 1, y1, m);
          int ps2 = partialSum(y1 + 1, 1, n, x1);
          int ps3 = partialSum(y1 + 1, x1 + 1, n, m);

          ans = Math.max(ans, (long)ps1 * ps2 *ps3);
        }
      }
    }

    System.out.println(ans);
  }   //  main-end

  //    좌상단 (y1, x1), 우하단 (y2, x2)인 직사각형 부분합 반환
  private static int partialSum(int y1, int x1, int y2, int x2) {
    return prefSum[y2][x2] - prefSum[y2][x1 - 1] - prefSum[y1 - 1][x2] + prefSum[y1 - 1][x1 - 1];
  }
}   //  Main-class-end
```

각 칸의 수는 최대 9이고, 칸의 개수는 최대 2,500개이므로 직사각형을 어떻게 잡아도 각 직사각형의 합은 int 범위이다. 하지만 세 직사각형 각각의 합을 곱한 값은 int 범위를 넘어설 수 있다.

모든 칸이 9이고 n = 50, m = 50이라고 가정하면 모든 칸의 합은 9 * 2,500이다.

세 직사각형의 크기를 동일하게 잡을 경우 각각의 합은 3 * 2,500이 된다. 세 직사각형의 각각의 합을 곱한 값은 int 범위를 초과하므로 이를 주의해야 한다.