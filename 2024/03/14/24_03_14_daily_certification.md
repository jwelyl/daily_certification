# 24_03_14_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 16500 문자열 판별**

[16500번: 문자열 판별](https://www.acmicpc.net/problem/16500)

**처음 접근**

주어진 문자열 s의 끝 end = length - 1에서부터 start까지 부분 문자열로 잘라서 해당 부분 문자열 [start:end]이 주어진 단어 목록에 존재할 경우 dp[start]를 true로 설정한 후 end를 start - 1로 재설정하여 반복하였다.

dp[i]를 [i:x] (x > i)가 단어 목록에 존재하면 true로 생각했다.

**예제 1**

![ex1.jpeg](24_03_14_daily_certification%20f54ea6405d184ec59c59b4b6fd8aec79/ex1.jpeg)

**실패한 코드 (시간 초과)**

```java
import java.util.*;
import java.io.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static String str;    //    주어진 문자열 s

  private static int n;         //    단어 개수
  private static Set<String> wordSet = new HashSet<String>();    //    단어 저장 리스트
  private static boolean[] dp;    //    dp[i] : str의 i부터의 부분문자열이 wordSet에 포함되면 dp[i] = true

  public static void main(String[] args) throws IOException {
    str = br.readLine();
    n = Integer.parseInt(br.readLine());

    dp = new boolean[str.length()];

    for(int i = 0; i < n; i++)
      wordSet.add(br.readLine());

    int end = str.length() - 1;

    while(end >= 0) {
      for(int start = end; start >= 0; start--) {
        String substr = str.substring(start, end + 1);    //    [start, end] 부분 문자열

        if(wordSet.contains(substr)) {    //    [start, end] 부분 문자열이 단어 목록에 존재할 경우
          dp[start] = true;    //    str의 start부터 end까지의 부분문자열이 wordSet에 포함됨
          end = start - 1;    //    end 수정
          break;
        }
      }
    }

    System.out.println(dp[0] ? 1 : 0);
  }   // main-end
}   //  Main-class-end
```

위의 코드의 경우 다음과 같은 반례에서 무한 루프를 돌게 된다.

```
softwarecontest
3
software
cont
ntest
```

최초에 [10:14]가 ntest이므로 이는 단어 목록에 존재한다. end = 9가 되는데 [start:end]가 단어 목록에 포함되는 start가 존재하지 안흐므로 end가 갱신되지 않아 무한 루프에 빠지게 된다.

**실패한 코드 (틀렸습니다)**

```java
import java.util.*;
import java.io.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static String str;    //    주어진 문자열 s

  private static int n;         //    단어 개수
  private static Set<String> wordSet = new HashSet<String>();    //    단어 저장 리스트
  private static boolean[] dp;    //    dp[i] : str의 i부터의 부분문자열이 wordSet에 포함되면 dp[i] = true

  public static void main(String[] args) throws IOException {
    str = br.readLine();
    n = Integer.parseInt(br.readLine());

    dp = new boolean[str.length()];

    for(int i = 0; i < n; i++)
      wordSet.add(br.readLine());

    int end = str.length() - 1;

    while(end >= 0) {
      boolean ok = false;
      for(int start = end; start >= 0; start--) {
        String substr = str.substring(start, end + 1);

        if(wordSet.contains(substr)) {    //    [start, end] 부분 문자열이 단어 목록에 존재할 경우
          dp[start] = true;    //    str의 start부터 end까지의 부분문자열이 wordSet에 포함됨
          end = start - 1;    //    end 수정
          ok = true;
          break;
        }
      }

      if(!ok)
        break;
    }

    System.out.println(dp[0] ? 1 : 0);
  }   // main-end
}   //  Main-class-end
```

단 한번도 매칭되지 않으면 break로 while문을 빠져나가게 작성했다. 위의 시간 초과반례는 해결된다.

하지만 다음과 같은 반례에 의해 틀리게 된다.

```
aba
2
a
ba
```

![contra.jpeg](24_03_14_daily_certification%20f54ea6405d184ec59c59b4b6fd8aec79/contra.jpeg)

a가 먼저 단어 목록에서 검색되므로 end = 1로 변경되게 되고 ab와 주어진 단어 목록을 비교하게 되므로 존재하지 않는다고 나온다. 하지만 a와 ba를 이어 만들 수 있으므로 정답이 아니다.

올바르게 답을 구하려면 dp[i] = ([i:]가 단어 목록에 존재하거나, 단어 목록에 존재하는 단어들을 이어서 만들 수 있으면 true) 로 놓고 문제를 해결해야 한다.

즉 dp[i]는 [i:]가 단어 목록에 존재하면 true, 만약 존재하지 않을 경우 [i:j], [j:i]로 나누어서 dp[j]가 true이고 [i:j]가 단어 목록에 존재해도 true가 된다.

따라서 dp[0]이 true면 주어진 문자열 s는 단어 목록의 단어들의 조합으로 나타낼 수 있다.

**정답 코드**

```java
import java.util.*;
import java.io.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static String str;    //    주어진 문자열 s

  private static int n;         //    단어 개수
  private static Set<String> wordSet = new HashSet<String>();    //    단어 저장 리스트
  private static boolean[] dp;    //    dp[i] : str의 i부터의 부분문자열이 wordSet의 단어 조합으로 만들어질 수 있으면 true

  public static void main(String[] args) throws IOException {
    str = br.readLine();
    n = Integer.parseInt(br.readLine());

    dp = new boolean[str.length()];

    for(int i = 0; i < n; i++)
      wordSet.add(br.readLine());

    for(int i = str.length() - 1; i >= 0; i--) {
      for(int j = i + 1; j < str.length(); j++) {
        if(dp[j]) { //  str의 j index부터의 부분문자열을 단어 목록 안의 단어들의 조합으로 만들 수 있다면
          if(wordSet.contains(str.substring(i, j))) //  str의 i index부터 j-1 index까지의 부분문자열이 단어 목록에 저장되어 있다면
            dp[i] = true; //  str의 i index부터의 부분문자열을 단어 목록 안의 단어들의 조합으로 만들 수 있음
        }
      }

      if(wordSet.contains(str.substring(i)))  //  str의 i index부터의 부분문자열이 단어 목록에 저장되어 있다면
        dp[i] = true;
    }

    //  str이 단어 목록 안의 단어들의 조합으로 만들어질 수 있으면 true
    System.out.println(dp[0] ? 1 : 0);
  }   // main-end
}   //  Main-class-end
```
