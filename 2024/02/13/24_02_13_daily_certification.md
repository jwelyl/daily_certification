# 24_02_13_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 2866 문자열 잘라내기?**

[2866번: 문자열 잘라내기](https://www.acmicpc.net/problem/2866)

브루트포스로 1행부터 R - 1행까지 한 행씩 지워가면서 문자열의 중복을 확인하는 코드는 다음과 같다.

**시간 초과 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2866 {
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

        for(i in 1 ..< r) {
            set.clear()
            for(j in 0 ..< c) {
                sb.clear()
                for(row in i ..< r)
                    sb.append(table[row][j])
                set.add(sb.toString())
            }

            if(set.size < c) break
            count++
        }

        println(count)
    }
}

fun main() {
    BOJ_2866().solve()
}
```

R, C가 1,000 이하의 자연수이므로 위 코드의 시간 복잡도는 O(R^2 * C)이므로 당연히 시간초과가 발생한다. 작성하면서도 시간 초과가 발생할 것이라는 생각 자체를 못하고 의기양양하게 제출했던 것이 부끄럽다.

0행부터 mid개의 행을 지웠을 때 중복 문자열이 발생하지 않는 최대 mid를 찾으면 된다. start = 0, end = r - 1로 하여 mid = (start + end) / 2로 놓고 중복이 발생하지 않으면 mid를 더 키워주고, 중복이 발생하면 mid를 줄이는 방법으로 최대 mid를 매개변수 탐색으로 찾는다.

시간복잡도는 O(R * C * logR)이다. R, C 모두 1,000 이하이므로 충분히 가능한 시간복잡도이다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2866 {
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
    BOJ_2866().solve()
}
```

이분탐색, 매개변수 탐색을 힌트를 보고 풀어낸 건 어쩔수 없더라도 브루트포스로 코드 작성 후 시간복잡도, 공간복잡도 검토도 없이 바로 제출한 것이 부끄럽다.

# 독서

**내가 틀릴 수도 있습니다. -비욘나티코린데블라드-**

**Q. 오늘 읽은 부분 중 기억에 남는 문장?**

**A. 머릿속에 떠오른 생각을 다 믿지는 않게 되었습니다.**