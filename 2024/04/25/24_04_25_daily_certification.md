# 24_04_25_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 숫자 등장 횟수**

[https://www.codetree.ai/missions/8/problems/number-frequency/introduction](https://www.codetree.ai/missions/8/problems/number-frequency/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/number-frequency/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static int n;   //  원소 개수
    private static int m;   //  질의 개수

    private static final Map<Integer, Integer> map = new HashMap<>();   //  K : 수열의 수, V : 수열의 수의 등장 횟수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());

        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            int num = Integer.parseInt(tokens.nextToken());
            int cnt = map.getOrDefault(num, 0);

            map.put(num, cnt + 1);
        }

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < m; i++)
            sb.append(map.getOrDefault(Integer.parseInt(tokens.nextToken()), 0)).append(" ");

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 가장 많은 데이터**

[https://www.codetree.ai/missions/8/problems/most-frequent-data/description](https://www.codetree.ai/missions/8/problems/most-frequent-data/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/most-frequent-data/description)

**코드**

```java
import java.util.*;
import java.io.*;
import java.util.Map.Entry;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static int n;   //  문자열 개수
  private static int maxCnt;

  private static final Map<String, Integer> map = new HashMap<>();   //  K : 문자열, V : 수열의 수의 등장 횟수

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    for(int i = 0; i < n; i++) {
      String str = br.readLine();

      map.put(str, map.getOrDefault(str, 0) + 1);
      maxCnt = Math.max(maxCnt, map.get(str));
    }

    System.out.println(maxCnt);
  }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 대응되는 수와 문자**

[https://www.codetree.ai/missions/8/problems/corresponding-numbers-and-characters/description](https://www.codetree.ai/missions/8/problems/corresponding-numbers-and-characters/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/corresponding-numbers-and-characters/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static int n;   //  숫자 개수
    private static int m;   //  조사 개수

    private static final Map<String, Integer> map1 = new HashMap<>();   //  K : 문자열, V : 숫자
    private static final Map<Integer, String> map2 = new HashMap<>();   //  K : 숫자, V : 문자열

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());

        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        for(int i = 1; i <= n; i++) {
            String str = br.readLine();
            map1.put(str, i);
            map2.put(i, str);
        }

        for(int i = 1; i <= m; i++) {
            String str = br.readLine();
            if('0' <= str.charAt(0) && str.charAt(0) <= '9')  //  숫자일 경우
                sb.append(map2.get(Integer.parseInt(str))).append("\n");
            else    //  문자열일 경우
                sb.append(map1.get(str)).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```