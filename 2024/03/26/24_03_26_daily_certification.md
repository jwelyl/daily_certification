# 24_03_26_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 2179 비슷한 단어**

[2179번: 비슷한 단어](https://www.acmicpc.net/problem/2179)

정말 놀랍게도 Bruteforcing으로 모든 단어쌍을 전부 비교해봐도 문제가 해결된다. 코드 자체는 훨씬 깔끔해지기도 하고 말이다.

**코드 (Bruteforcing)**

```kotlin
import java.util.*
import java.io.*

class BOJ_2179 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private var n = 0

    private val wordList = ArrayList<String>()

    private var maxMatch = -1
    private var maxMatchStrIdx1 = 0
    private var maxMatchStrIdx2 = 0

    fun solve() {
        n = br.readLine().toInt()

        repeat(n) {
            wordList.add(br.readLine())
        }

        for(i in 0 ..< n - 1) {
            val s1 = wordList[i]

            for(j in i + 1 ..< n) {
                val s2 = wordList[j]
                var match = 0

                for(k in 0 ..< s1.length.coerceAtMost(s2.length)) {
                    if(s1[k] == s2[k])
                        match++
                    else
                        break
                }

                if(match > maxMatch) {
                    maxMatch = match
                    maxMatchStrIdx1 = i
                    maxMatchStrIdx2 = j
                }
            }
        }

        println(wordList[maxMatchStrIdx1])
        println(wordList[maxMatchStrIdx2])
    }
}

fun main() {
    BOJ_2179().solve()
}
```

하지만 n이 조금만 더 컸어도 이 방법은 불가능할 것을 짐작할 수 있다. 좀 더 최적의 시간 복잡도로 문제를 해결해보자.

![ex0102.jpeg](24_03_26_daily_certification%20878eedac9e2143e6b6d53c33a6169ea6/ex0102.jpeg)

예제 1, 2를 그림으로 나타낸 것이다. 주어진 단어들을 사전순으로 정렬한다. 이 때 원래 주어진 순서를 기억하기 위해 Map을 만들어 단어의 주어진 순서를 기억하도록 한다. 같은 단어는 주어지지 않으므로 가능하다.

사전순으로 정렬된 리스트의 서로 인접한 두 단어를 확인하면서 최대 접두사 길이를 구한다. 최대 접두사 길이가 갱신될 경우 두 단어를 S, T로 하되, Map을 확인해 두 단어의 순서를 정하여 S, T를 정한다. 최대 접두사 길이와 현재 두 단어의 접두사 길이가 같을 경우, 이전에 결정된 두 단어와 현재 두 단어의 순서들을 비교해서 S, T를 정한다.

**실패한 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2179 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private var n = 0

    private val wordList = ArrayList<String>()            //    단어 저장 리스트
    private val wordMap = HashMap<String, Int>()      //    Key : 단어, Value : 정렬 전 단어 index

    private var maxMatch = -1
    private var maxMatchStr1 = ""
    private var maxMatchStr2 = ""
    private var maxMatchIdx1 = 0
    private var maxMatchIdx2 = 0

    fun solve() {
        n = br.readLine().toInt()

        repeat(n) {
            val word = br.readLine()
            wordList.add(word)       //  리스트에 단어 추가
            wordMap.put(word, it)    //  맵에 단어, 주어진 순서 추가
        }

        wordList.sort()    //    단어 사전 순으로 정렬

        for(i in 0 ..< n - 1) {
            val s1 = wordList[i]
            val s2 = wordList[i + 1]

            var match = 0    //    인접한 두 단어가 매치되는 문자 수

            for(j in 0 ..< s1.length.coerceAtMost(s2.length)) {
                if(s1[j] == s2[j])
                    match++
            }

            if(match > maxMatch) {    //    최대 매칭 길이 갱신할 경우
                maxMatch = match
                val idx1 = wordMap[s1]!!
                val idx2 = wordMap[s2]!!

                if(idx1 < idx2) {
                    maxMatchStr1 = s1
                    maxMatchIdx1 = idx1
                    maxMatchStr2 = s2
                    maxMatchIdx2 = idx2
                } else {
                    maxMatchStr1 = s2
                    maxMatchIdx1 = idx2
                    maxMatchStr2 = s1
                    maxMatchIdx2 = idx1
                }
            }
            else if(match == maxMatch) {
                val idx1 = wordMap[s1]!!
                val idx2 = wordMap[s2]!!

                var candIdx1 = 0
                var candIdx2 = 0
                var candStr1 = ""
                var candStr2 = ""

                if(idx1 < idx2) {
                    candIdx1 = idx1
                    candStr1 = s1
                    candIdx2 = idx2
                    candStr2 = s2
                } else {
                    candIdx1 = idx2
                    candStr1 = s2
                    candIdx2 = idx1
                    candStr2 = s1
                }

                if(candIdx1 < maxMatchIdx1) {
                    maxMatchStr1 = candStr1
                    maxMatchStr2 = candStr2
                    maxMatchIdx1 = candIdx1
                    maxMatchIdx2 = candIdx2
                } else if(candIdx1 == maxMatchIdx1 && candIdx2 < maxMatchIdx2) {
                    maxMatchStr1 = candStr1
                    maxMatchStr2 = candStr2
                    maxMatchIdx1 = candIdx1
                    maxMatchIdx2 = candIdx2
                }
            }
        }

        println(maxMatchStr1)
        println(maxMatchStr2)
    }
}

fun main() {
    BOJ_2179().solve()
}
```

하지만 이 경우 정답을 받지 못한다. 구현하면서 코드가 너무 길어지면서 이상함을 느끼기도 했지만 다음과 같은 명백한 반례가 존재한다.

![counter1.jpeg](24_03_26_daily_certification%20878eedac9e2143e6b6d53c33a6169ea6/counter1.jpeg)

위 코드의 맹점은 정렬된 단어 리스트에서 인접한 두 단어끼리만 최대 접두사를 찾는다는 것이다. 최대 접두사를 가지는 아무 두 단어나 출력해도 되면 상관없겠지만, 더 먼저 입력된 두 단어를 사용해야 하므로 정렬 후 인접한 두 단어만 비교해서는 안된다.

위의 예시에서 출력값 다음과 같다.

```
abcdab
abcdaa
```

하지만 기대되는 정답은 다음과 같다.

```
abcdab
abcda
```

일단 최대 접두사를 가지는 모든 단어들을 리스트에 저장할 필요가 있다. 그 후 해당 단어들을 입력 순으로 정렬하여 가장 먼저 들어온 단어인 첫 번째 단어를 S, S와 최대 접두사로 매칭되는 단어 중 그 다음으로 빨리 들어온 단어를 T로 하면 된다.

다음과 같은 예시를 생각해보자.

```
7
xyzw
abcdaa
abcxab
abcyx
abcd
xyzwhi
xyzwaa
```

기대되는 정답은 다음과 같다.

```
xyzw
xyzwhi
```

먼저 주어진 단어들을 사전순으로 정렬한다.

![myex1.jpeg](24_03_26_daily_certification%20878eedac9e2143e6b6d53c33a6169ea6/myex1.jpeg)

다음으로 정렬된 리스트를 확인하며 인접한 두 단어쌍의 접두사 길이를 구한다. 접두사 길이가 최대 접두사 길이와 같은 단어쌍들을 따로 리스트에 저장하는데, 이 때 중복된 단어가 들어가지 않도록 Set을 이용해 체크한다.

위의 예제에서는 확인할 수 없지만, 최대 접두사 길이가 더 긴 것으로 갱신될 때는 기존에 저장된 단어쌍 리스트와 중복 체크에 사용된 Set을 모두 비워줘야 한다.

![myex2.jpeg](24_03_26_daily_certification%20878eedac9e2143e6b6d53c33a6169ea6/myex2.jpeg)

**리스트에 저장된 어떤 한 단어는, 반드시 자신과 최대 접두사를 가지는 다른 단어도 리스트에 존재한다.**

리스트를 단어가 들어온 순으로 정렬한다.

![myex3.jpeg](24_03_26_daily_certification%20878eedac9e2143e6b6d53c33a6169ea6/myex3.jpeg)

정렬된 리스트의 첫 번째 단어는 최대 접두사를 가진 단어 중 가장 먼저 들어온 단어가 되므로 S가 되고, 순차적으로 리스트를 순회하면서 첫 번째 단어와 최대 접두사를 가지는 단어를 최초로 만나면 해당단어를 T로 하고 종료하면 된다. **리스트에 저장된 어떤 한 단어는, 반드시 자신과 최대 접두사를 가지는 다른 단어도 리스트에 존재한다.**

**정답 코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static int n; //  단어 개수
  private final static Map<String, Integer> wordOrder = new HashMap<>();  //  단어별 입력 순서 저장한 map
  private final static List<String> wordList = new ArrayList<>();         //  단어 저장 후 정렬할 리스트

  private static int maxMatch = 0;  //  접두사 최대 매칭 길이
  private final static List<String> resultList = new ArrayList<>();       //  최대 매칭되는 단어들 저장할 리스트
  private final static Set<String> resultSet = new HashSet<>();           //  최대 매칭되는 단어가 중복되지 않도록

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    for (int i = 0; i < n; i++) {
      String word = br.readLine();
      wordList.add(word);
      wordOrder.put(word, i);
    }

    Collections.sort(wordList);

    for (int i = 0; i < n - 1; i++) {
      String s1 = wordList.get(i);
      String s2 = wordList.get(i + 1);

      int match = 0;

      for (int j = 0; j < Math.min(s1.length(), s2.length()); j++) {
        if (s1.charAt(j) != s2.charAt(j)) {
          break;
        }

        match++;
      }

      if (match > maxMatch) { //  새로운 최대 매칭 길이를 찾았을 경우
        resultList.clear(); //  이전 최대 매칭 단어들 모두 제거
        resultSet.clear();
        resultSet.add(s1);
        resultSet.add(s2);
        resultList.add(s1);
        resultList.add(s2); //  새로운 최대 매칭 단어들 추가

        maxMatch = match; //  최대 매칭 길이 갱신
      } else if (match == maxMatch) {
        if (!resultSet.contains(s1)) {
          resultSet.add(s1);
          resultList.add(s1);
        }
        if (!resultSet.contains(s2)) {
          resultSet.add(s2);
          resultList.add(s2);
        }
      }
    }

    resultList.sort((s1, s2) -> {
      int idx1 = wordOrder.get(s1);
      int idx2 = wordOrder.get(s2);

      return Integer.compare(idx1, idx2);
    });

    String s1 = resultList.get(0);
    String prefix = s1.substring(0, maxMatch);
    String s2 = "";

    for (int i = 1; i < resultList.size(); i++) {
      if (resultList.get(i).startsWith(prefix)) {
        s2 = resultList.get(i);
        break;
      }
    }

    System.out.println(s1);
    System.out.println(s2);
  } // main-end
} // Main-class-end
```