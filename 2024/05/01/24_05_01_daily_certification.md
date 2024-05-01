# 24_05_01_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 원소의 합이 0**

[https://www.codetree.ai/missions/8/problems/the-sum-of-the-elements-is-0/description](https://www.codetree.ai/missions/8/problems/the-sum-of-the-elements-is-0/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-sum-of-the-elements-is-0/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;

  private static final Map<Integer, Integer> map = new HashMap<>();
  private static int n;
  private static int[] a, b, c, d;

  private static long ans = 0L;

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    a = new int[n];
    b = new int[n];
    c = new int[n];
    d = new int[n];

    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      a[i] = Integer.parseInt(tokens.nextToken());
    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      b[i] = Integer.parseInt(tokens.nextToken());
    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      c[i] = Integer.parseInt(tokens.nextToken());
    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      d[i] = Integer.parseInt(tokens.nextToken());

    for(int i = 0; i < n; i++) {
      for(int j = 0; j < n; j++) {
          int sum = a[i] + b[j];
          int comp = -sum;

          map.put(comp, map.getOrDefault(comp, 0) + 1);
      }
    }

    for(int i = 0; i < n; i++) {
      for(int j = 0; j < n; j++) {
        int sum = c[i] + d[j];

        ans += map.getOrDefault(sum, 0);
      }
    }

    System.out.println(ans);
  }   //  main-end
}   //  Main-class-end
```