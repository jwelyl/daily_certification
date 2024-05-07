# 24_05_08_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 차이가 가장 작은 수**

[https://www.codetree.ai/missions/8/problems/the-number-with-the-smallest-difference/description](https://www.codetree.ai/missions/8/problems/the-number-with-the-smallest-difference/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-number-with-the-smallest-difference/description)

**코드 (투 포인터)**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  private static final int NONE = Integer.MAX_VALUE;
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static int n; //  수의 개수
  private static int m; //  두 수의 차이가 m 이상이어야 함

  private static int[] nums;  //  주어진 수
  private static int ans = NONE;  //  두 수의 차이가 m 이상이면서 최소인 차이, m 이상인 차이가 없으면 NONE

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    nums = new int[n];
    for(int i = 0; i < n; i++)
      nums[i] = Integer.parseInt(br.readLine());

    Arrays.sort(nums);

    int start = 0;
    int end = 1;

    while(start < end && end < n) {
      int diff = nums[end] - nums[start];

      if(diff >= m) {  //  두 수의 차이가 m 이상이면
        ans = Math.min(ans, nums[end] - nums[start]); //  두 수의 차이 더 작은 값으로 갱신
        start++;  //  더 작은 차이가 있나 확인하기 위해 start 키워서 차이 줄여보기
        if(start == end)  //  만약 start가 end를 따라잡으면 둘이 같은 수가 됨
          end++;  //  end 더 키워서 서로 다른 수가 되게 하기
      }
      else //  두 수 차이가 m 미만이면
        end++;  //  더 큰 차이가 있나 확인하기 위해 end 키워서 차이 크게 하기
    }

    System.out.println(ans == NONE ? -1 : ans);
  } // main-end
} //  Main-class-end
```

**코드 (TreeSet)**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
  private static final int NONE = Integer.MAX_VALUE;
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static int n; //  수의 개수
  private static int m; //  두 수의 차이가 m 이상이어야 함

  private static final TreeSet<Integer> treeSet = new TreeSet<>();
  private static int[] nums;  //  주어진 수
  private static int ans = NONE;  //  두 수의 차이가 m 이상이면서 최소인 차이, m 이상인 차이가 없으면 NONE

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    nums = new int[n];
    for(int i = 0; i < n; i++) {
      nums[i] = Integer.parseInt(br.readLine());
      treeSet.add(nums[i]);
    }

    for(int i = 0; i < n; i++) {
      int num = nums[i];

      Integer left = treeSet.floor(num - m);      //  num보다 m 이상 작으면서 가장 큰 수
      Integer right = treeSet.ceiling(num + m);   //  num보다 m 이상 크면서 가장 작은 수

      int leftDiff = left == null ? NONE : num - left;
      int rightDiff = right == null ? NONE : right - num;

      ans = Math.min(ans, Math.min(leftDiff, rightDiff));
    }

    System.out.println(ans == NONE ? -1 : ans);
  } // main-end
} //  Main-class-end
```

**CO{)E TREE 점 빼기**

[https://www.codetree.ai/missions/8/problems/remove-point/description](https://www.codetree.ai/missions/8/problems/remove-point/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/remove-point/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
  private static final int NONE = Integer.MAX_VALUE;
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;
  private static int n; //  점의 개수
  private static int m; //  질의 개수

  private static final TreeSet<Point> treeSet = new TreeSet<>();

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    for(int i = 0; i < n; i++) {
      tokens = new StringTokenizer(br.readLine());
      treeSet.add(new Point(Integer.parseInt(tokens.nextToken()), Integer.parseInt(tokens.nextToken())));
    }

    for(int i = 0; i < m; i++) {
      int k = Integer.parseInt(br.readLine());

      Point p = treeSet.ceiling(new Point(k, 0)); //  x 좌표가 k인 점보다 x 좌표가 크거나 같은 점 찾기

      if(p == null) //  그런 점이 없을 경우
        sb.append("-1 -1\n");
      else {  //  그런 점이 있을 경우
        sb.append(p.x).append(" ").append(p.y).append("\n");  //  출력하고
        treeSet.remove(p);  //  지우기
      }
    }

    System.out.println(sb);
  } // main-end

  private static class Point implements Comparable<Point> {
    int x;
    int y;

    public Point(int x, int y) {
      this.x = x;
      this.y = y;
    }

    @Override
    public int compareTo(Point p) {
      int ret = Integer.compare(this.x, p.x); //  x 값이 작은 점 먼저

      if(ret == 0)  //  x 값이 같을 경우
        ret = Integer.compare(this.y, p.y); //  y 값이 작은 점 먼저

      return ret;
    }
  }
} //  Main-class-end

```

**CO{)E TREE 행렬로 주어진 간선**

[https://www.codetree.ai/missions/8/problems/edge-given-by-matrix/description](https://www.codetree.ai/missions/8/problems/edge-given-by-matrix/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/edge-given-by-matrix/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;
  private static int n; //  정점의 개수

  private static int[][] dp;  //  그래프 상태

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    dp = new int[n + 1][n + 1];

    for(int i = 1; i <= n; i++) {
      tokens = new StringTokenizer(br.readLine());
      for(int j = 1; j <= n; j++) {
        dp[i][j] = Integer.parseInt(tokens.nextToken());
        if(i == j)
          dp[i][j] = 1;
      }
    }

    floydWarshall();

    for(int i = 1; i <= n; i++) {
      for(int j = 1; j <= n; j++)
        sb.append(dp[i][j]).append(" ");
      sb.append("\n");
    }

    System.out.print(sb);
  } // main-end

  private static void floydWarshall() {
    for(int k = 1; k <= n; k++) {
      for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
          if(dp[i][k] == 1 && dp[k][j] == 1)
            dp[i][j] = 1;
        }
      }
    }
  }
} //  Main-class-end

```