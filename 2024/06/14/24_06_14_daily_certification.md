# 24_06_14_daily_certificatio

# Problem Solving (Algorithm & SQL)

### BOJ 4095 최대 정사각형

[](https://www.acmicpc.net/problem/4095)

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0

    private lateinit var map : Array<BooleanArray>
    private lateinit var dp : Array<IntArray>
    private var max = 0

    fun solve() {
        while(true) {
            max = 0
            tokens = StringTokenizer(br.readLine())
            n = tokens.nextToken().toInt()
            m = tokens.nextToken().toInt()

            if(n == 0 && m == 0) {
                print(sb)
                break
            }

            map = Array(n + 1) { BooleanArray(m + 1) }
            dp = Array(n + 1) { IntArray(m + 1) }

            for(i in 1 .. n) {
                tokens = StringTokenizer(br.readLine())
                for(j in 1 .. m)
                    map[i][j] = tokens.nextToken().toInt() == 1
            }

            for(i in 1 .. n) {
                for(j in 1 .. m) {
                    if(map[i][j]) {
                        dp[i][j] = minOf(dp[i - 1][j - 1], dp[i][j - 1], dp[i - 1][j]) + 1
                        max = maxOf(max, dp[i][j])
                    }
                    else
                        dp[i][j] = 0
                }
            }

            sb.append("$max\n")
        }
    }
}

fun main() {
    Main().solve()
}
```