# 24_06_27_daily_certification

# Computer Architecture

# Problem Solving (Algorithm & SQL)

### BOJ 14658 **하늘에서 별똥별이 빗발친다**

[](https://www.acmicpc.net/problem/14658)

```kotlin
import java.util.*
import java.io.*

class BOJ_14658 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var m = 0   //  별똥별 떨어지는 범위 (0 <= x <= n, 0 <= y <= m)
    private var l = 0   //  트램펄린 한 변 길이
    private var k = 0   //  별똥별 개수

    private val stars = ArrayList<IntArray>()   //  별똥별

    private var ans = 0 //  최대로 받을 수 있는 별똥별 수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        l = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        repeat(k) {
            tokens = StringTokenizer(br.readLine())
            stars.add(intArrayOf(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        for(star1 in stars) {
            for(star2 in stars) {
                val cx = star1[0]
                val cy = star2[1]

                var cnt = 0

                for(star in stars) {
                    val x = star[0]
                    val y = star[1]

                    if((x in cx .. cx + l) && (y in cy .. cy + l))
                        cnt++
                }

                ans = maxOf(ans, cnt)
            }
        }

        println(k - ans)
    }
}

fun main() {
    BOJ_14658().solve()
}
```