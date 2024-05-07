# 24_05_07_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 가까운 숫자**

[https://www.codetree.ai/missions/8/problems/nearest-number/description](https://www.codetree.ai/missions/8/problems/nearest-number/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/nearest-number/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static final StringBuilder sb = new StringBuilder();

  private static int n;   //  점 개수
  private static final TreeSet<Integer> treeSet = new TreeSet<>();

  public static void main(String[] args) throws IOException {
    treeSet.add(0);

    n = Integer.parseInt(br.readLine());

    tokens = new StringTokenizer(br.readLine());

    int point = Integer.parseInt(tokens.nextToken());
    treeSet.add(point);
    sb.append(point).append("\n");

    int low = treeSet.first();  //  가장 가까운 두 점의 작은 점
    int high = treeSet.last();  //  가장 가까운 두 점의 큰 점

    for(int i = 0; i < n - 1; i++) {
      point = Integer.parseInt(tokens.nextToken());

      treeSet.add(point);

      Integer lowerPoint = treeSet.lower(point);    //  현재 입력받은 점보다 작으면서 가장 큰 점
      Integer higherPoint = treeSet.higher(point);  //  현재 입력받은 점보다 크면서 가장 작은 점

      int minDist = high - low; //  이전에 알려진 가장 가까운 두 점 사이 거리
      int leftMinDist;
      int rightMinDist;

      if(lowerPoint != null && higherPoint != null) {
        leftMinDist = point - lowerPoint;     //  현재 입력받은 점과 그 점보다 작은 가장 가까운 점
        rightMinDist = higherPoint - point;   //  현재 입력받은 점과 그 점보다 큰 가장 가까운 점
      }
      else if(lowerPoint == null) { //  현재 입력받은 점보다 작은 점이 없을 경우
        leftMinDist = Integer.MAX_VALUE;
        rightMinDist = higherPoint - point;
      }
      else {  //  현재 입력받은 점보다 큰 점이 없을 경우
        leftMinDist = point - lowerPoint;
        rightMinDist = Integer.MAX_VALUE;
      }

      if(leftMinDist > rightMinDist) {
        if(minDist > rightMinDist) {  //  이전에 알려진 가장 가까운 두 점 사이 거리보다 가까울 경우
          low = point;
          high = higherPoint;   //  가장 가까운 두 점 갱신
          minDist = rightMinDist;
        }
      }
      else {
        if(minDist > leftMinDist) {
          low = lowerPoint;
          high = point;
          minDist = leftMinDist;
        }
      }

      sb.append(minDist).append("\n");
    }

    System.out.print(sb);
  } // main-end
} // Main-class-end
```

**CO{)E TREE top k 숫자**

[https://www.codetree.ai/missions/8/problems/top-k-elements/description](https://www.codetree.ai/missions/8/problems/top-k-elements/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/top-k-elements/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static final StringBuilder sb = new StringBuilder();

  private static int n;   //  수 개수
  private static int k;   //  상위 k개 수
  private static final TreeSet<Integer> treeSet = new TreeSet<>(Collections.reverseOrder());

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    k = Integer.parseInt(tokens.nextToken());

    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      treeSet.add(Integer.parseInt(tokens.nextToken()));

    for(int i = 0; i < k; i++) {
      int top = treeSet.first();
      treeSet.remove(top);

      sb.append(top).append(" ");
    }

    System.out.println(sb);
  } // main-end
} // Main-class-end
```

**CO{)E TREE 자리 차지하기**

[https://www.codetree.ai/missions/8/problems/take-place/description](https://www.codetree.ai/missions/8/problems/take-place/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/take-place/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static int n;   //  사람 수
  private static int m;   //  비어있는 의자 수
  private static final TreeSet<Integer> treeSet = new TreeSet<>();  //  앉을 수 있는 자리

  private static int ans = 0; //  최대로 앉을 수 있는 사람 수

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    for(int i = 1; i <= m; i++)
      treeSet.add(i);

    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++) {
      int pos = Integer.parseInt(tokens.nextToken()); //  i+1번째 사람이 앉고 싶어하는 자리 (1 이상, pos 이하 자리)
      Integer leftPos = treeSet.floor(pos); //  pos보다 작거나 같은 자리 중 앉을 수 있는 자리

      if(leftPos == null)  //  pos 이하 자리 중 앉을 수 있는 자리가 없을 경우, 앉을 수 없음
        break;
      else {  //  pos 이하 자리 중 앉을 수 있는 자리가 있을 경우
        ans++;  //  i+1번째 사람은 앉을 수 있음
        treeSet.remove(leftPos);  //  해당 자리를 앉을 수 없게 표시
      }
    }

    System.out.println(ans);
  } // main-end
} // Main-class-end
```