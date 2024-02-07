# 24_02_08_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 13144 List of Unique Numbers**

[13144번: List of Unique Numbers](https://www.acmicpc.net/problem/13144)

풀이는 그림으로 대체한다. 어차피 나 혼자 볼거니까 ㅎㅎ

![2P1.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P1.png)

![2P2.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P2.png)

![2P3.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P3.png)

중복이 발생할 때까지 end 포인터를 이동시키면서 end 포인터로 끝나는 부분 수열들을 더해준다.

중복이 발생할 경우 중복이 사라질 때까지 start 포인털르 이동시킨다.

중복 체크는 visited 배열을 사용했다. 가능한 수가 최대 100,000이므로 가능했는데 만약 더 큰 수의 중복 체크가 필요하다면 HashSet을 사용할 수 있을 것 같다.

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 100_001

class BOJ_13144 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var sequence : IntArray
    private val visited = BooleanArray(MAX)

    private var ans = 0L

    fun solve() {
        n = br.readLine().toInt()
        sequence = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            sequence[i] = tokens.nextToken().toInt()

        var start = 0
        var end = 0

        while(end < n) {
            while(visited[sequence[end]]) {
                visited[sequence[start]] = false
                start++
            }

            while(end < n && !visited[sequence[end]]) {
                ans += (end - start + 1)
                visited[sequence[end]] = true
                end++
            }
        }

        println(ans)
    }
}

fun main() {
    BOJ_13144().solve()
}
```