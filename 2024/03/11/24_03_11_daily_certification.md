# 24_03_11_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1027 고층 건물**

[1027번: 고층 건물](https://www.acmicpc.net/problem/1027)

빌딩 수가 최대 50개이므로, 각 빌딩마다 볼 수 있는 건물 수를 O(N)의 시간 복잡도에 파악 가능하고, 모든 빌딩에 대해서 계산해보면 되므로 O(N^2)의 시간 복잡도로 해결이 가능하다.

각 빌딩에 대해서 왼쪽에 있는 모든 빌딩, 오른쪽에 있는 모든 빌딩을 확인한다. 현재 빌딩에서 더 나중에 보려는 빌딩과의 기울기(절댓값)이 이전에 보려는 빌딩과의 절댓값보다 작거나 같을 경우, 이전 빌딩에 가려져서 볼 수 없다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1027 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    건물 개수
    private lateinit var heights : IntArray    //    건물 높이

    private var ans = Int.MIN_VALUE    //    한 고층 건물에서 볼 수 있는 다른 고층 건물 수의 최대

    fun solve() {
        n = br.readLine().toInt()
        heights = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            heights[i] = tokens.nextToken().toInt()

        for(i in 0 ..< n) {
            var cnt = 0

            var slope = -Double.MAX_VALUE
            for(j in i - 1 downTo 0) {    //    좌측에 있는 건물들 확인
                val newSlope = (heights[j] - heights[i]).toDouble() / (i - j)

                if(newSlope > slope) {
                    slope = newSlope
                    cnt++
                }
            }

            slope = -Double.MAX_VALUE
            for(j in i + 1 ..< n) {    //    우측에 있는 건물들 확인
                val newSlope = (heights[j] - heights[i]).toDouble() / (j - i)

                if(newSlope > slope) {
                    slope = newSlope
                    cnt++
                }
            }

            ans = ans.coerceAtLeast(cnt)
        }

        println(ans)
    }
}

fun main() {
    BOJ_1027().solve()
}
```
