# 24_03_07_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 11585 속타는 저녁 메뉴**

[11585번: 속타는 저녁 메뉴](https://www.acmicpc.net/problem/11585)

**예제 1**

![ex1.jpeg](24_03_07_daily_certification%206974ef5d10404853a3a7c0bd1ccf14bf/ex1.jpeg)

**예제 2**

![ex2.jpeg](24_03_07_daily_certification%206974ef5d10404853a3a7c0bd1ccf14bf/ex2.jpeg)

문제를 해결하기 위해 고기 당첨 룰렛 모양(코드에서 text)를 두 배로 늘린 후, 현재 원형 룰렛 모양(코드에서 pattern)을 KMP로 찾으면 된다.

![ex2_desc.jpeg](24_03_07_daily_certification%206974ef5d10404853a3a7c0bd1ccf14bf/ex2_desc.jpeg)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_11585 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var text : CharArray
    private lateinit var pattern : CharArray

    private lateinit var failure : IntArray //  실패 함수

    fun solve() {
        n = br.readLine().toInt()

        text = CharArray(2 *n)
        pattern = CharArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n) {
            text[i] = tokens.nextToken()[0]
            text[i + n] = text[i]
        }

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            pattern[i] = tokens.nextToken()[0]

        makeFailure()

        val match = kmp()   //  패턴 매칭 횟수
        val gcd = gcd(match, n)

        println("${match / gcd}/${n / gcd}")
    }

    private fun makeFailure() {
        failure = IntArray(n)

        var idx = 0

        for(i in 1 ..< n) {
            while(idx > 0 && pattern[i] != pattern[idx])
                idx = failure[idx - 1]

            if(pattern[i] == pattern[idx])
                failure[i] = ++idx
        }
    }

    private fun kmp() : Int {
        var idx = 0
        var match = 0

        for(i in 0 ..< 2 * n - 1) {
            while(idx > 0 && text[i] != pattern[idx])
                idx = failure[idx - 1]

            if(text[i] == pattern[idx]) {
                if(idx == n - 1) {
                    match++
                    idx = failure[idx]
                } else idx += 1
            }
        }

        return match
    }

    private fun gcd(num1 : Int, num2 : Int) : Int {
        var large = num2
        var small = num1

        while(true) {
            val r = large % small

            if(r == 0)
                break

            large = small
            small = r
        }

        return small
    }
}

fun main() {
    BOJ_11585().solve()
}
```
