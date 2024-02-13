# 24_02_14_daily_certification

```
[koreii] #45 데일리인증
20240214
JPA
- JPQL 기본 문법
알고리즘
- Greedy & Priority Queue 문제 풀이, 이전 비슷한 문제 복습
독서
```

# Problem Solving (Algorithm & SQL)

**BOJ 2109 순회강연**

[2109번: 순회강연](https://www.acmicpc.net/problem/2109)

이전에 풀었던 1781 컵라면 문제와 유사하다.

[1781번: 컵라면](https://www.acmicpc.net/problem/1781)

우선순위 큐에는 강연할 수 있는 강의들만 담겨 있다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled.png)

우선순위 큐의 크기가 곧 강연할 날의 수이다. 강연의 d보다 우선순위 큐의 크기가 크다면 해당 강연을 하기에는 이미 잡혀있는 강연이 너무 많은 것이다. 그럴 경우 해당 강연을 포함하여 기존에 잡혀있는 강연 중 가장 가치가 작은 강연부터 배제한다.

일단 강연 날짜가 촉박한 것부터 해결해 나가야 하므로 강연 날짜가 빠른 순으로 정렬한다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%201.png)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2109 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var r = 0
    private var c = 0
    private lateinit var table : Array<CharArray>
    private val set = HashSet<String>()
    private var count = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        r = tokens.nextToken().toInt()
        c = tokens.nextToken().toInt()

        table = Array(r) { CharArray(c) }
        for(i in 0 ..< r)
            table[i] = br.readLine().toCharArray()

        parametricSearch()
        println(count)
    }

    private fun parametricSearch() {
        var start = 0
        var end = r - 1

        while(start <= end) {
            val mid = (start + end) / 2 //  mid행부터 부분 문자열을 확인해봄

            set.clear()
            for(j in 0 ..< c) {
                sb.clear()
                for(i in mid ..< r)
                    sb.append(table[i][j])
                set.add(sb.toString())
            }

            if(set.size == c) { //  mid행부터 문자열 잘랐을 때 중복 없을 경우
                count = mid     //  일단 count 저장
                start = mid + 1 //  더 큰 count 찾아보기
            }
            else  //  mid행부터 문자열 잘랐을 때 중복 발생할 경우
                end = mid - 1   //  더 작은 count 찾아야 함
        }
    }
}

fun main() {
    BOJ_2109().solve()
}
```

# 독서

**내가 틀릴 수도 있습니다. -비욘나티코린데블라드-**

**Q. 오늘 읽은 부분 중 기억에 남는 문장?**

**A.**