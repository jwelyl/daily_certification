# 24_02_03_daily_certification

```
[koreii] #34 데일리인증
20240203
알고리즘
- 기하학 문제 풀이 (호도법, 코사인법칙)
```

# Problem Solving (Algorithm & SQL)

**BOJ 1798 원뿔좌표계상의 거리**

[1798번: 원뿔좌표계상의 거리](https://www.acmicpc.net/problem/1798)

P1, P2의 각을 각각 A1, A2라고 할 때 옆면의 부채꼴에서 선분 CP1과 선분 CP2가 이루는 각을 theta라고 하면 먼저 xy 평면에서 OP1’과 OP2’이 이루는 각 theta’을 구해야 한다. (그림 참고)

![1.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/1.jpeg)

![2.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/2.jpeg)

theta’을 구하면 부채꼴에서 CP1, CP2가 이루는 각을 구할 수 있다. 밑면의 원주 중 P1’P2’의 길이와 부채꼴에서의 호 중 P1’P2’의 길이가 같음을 이용하면 된다.

![3.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/3.jpeg)

theta를 호도법으로 변환해주고 제 2 코사인 법칙을 이용하면 정답을 구할 수 있다.

![4.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/4.jpeg)

재미있는 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.*

const val RAD = PI / 180

class BOJ_1798 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var r = 0.0
    private var h = 0.0
    private var d1 = 0.0
    private var a1 = 0.0
    private var d2 = 0.0
    private var a2 = 0.0

    private var l = 0.0 //  원뿔 빗변 길이
    private var theta = 0.0
    private var ans = 0.0

    fun solve() {
        while(true) {
            val input = br.readLine()
            if(input == null) {
                print(sb)
                break
            }

            tokens = StringTokenizer(input)
            r = tokens.nextToken().toDouble()
            h = tokens.nextToken().toDouble()
            d1 = tokens.nextToken().toDouble()
            a1 = tokens.nextToken().toDouble()
            d2  = tokens.nextToken().toDouble()
            a2 = tokens.nextToken().toDouble()

            l = sqrt(r * r + h * h)
            theta = abs(a2 - a1).coerceAtMost(abs(360 + a1 - a2))   //  60분법 중심각
            theta *= RAD //  radian
            theta = (r * theta) / l

            ans = d1 * d1 + d2 * d2 - 2 * d1 * d2 * cos(theta)
            ans = sqrt(ans)

            sb.append(String.format("%.2f\n", ans))
        }
    }
}

fun main() {
    BOJ_1798().solve()
}
```