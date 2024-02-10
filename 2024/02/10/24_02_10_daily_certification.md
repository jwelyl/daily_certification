# 24_02_10_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 2773 바깥 삼각형의 중심**

[2773번: 바깥 삼각형의 중심](https://www.acmicpc.net/problem/2773)

벡터의 내적을 이용한 수직인 벡터 찾기 & 중선 벡터 구하기 & 한 점과 방향 벡터가 주어질 때 직선의 방정식

![1.png](24_02_10_daily_certification%202e696e68d25846c3ad3214944bf2f9cf/1.png)

![2.png](24_02_10_daily_certification%202e696e68d25846c3ad3214944bf2f9cf/2.png)

![3.png](24_02_10_daily_certification%202e696e68d25846c3ad3214944bf2f9cf/3.png)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2773 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var t = 0
    private var x1 = 0.0
    private var y1 = 0.0
    private var x2 = 0.0
    private var y2 = 0.0
    private var x3 = 0.0
    private var y3 = 0.0

    private var s = 0.0
    private var x = 0.0
    private var y = 0.0

    fun solve() {
        t = br.readLine().toInt()

        repeat(t) {
            tokens = StringTokenizer(br.readLine())
            x1 = tokens.nextToken().toDouble()
            y1 = tokens.nextToken().toDouble()
            tokens = StringTokenizer(br.readLine())
            x2 = tokens.nextToken().toDouble()
            y2 = tokens.nextToken().toDouble()
            tokens = StringTokenizer(br.readLine())
            x3 = tokens.nextToken().toDouble()
            y3 = tokens.nextToken().toDouble()

            s = ((x3 - x2) * (x1 - x3) - (y3 - y2) * (y3 - y1)) / ((x2 - x1) * (y3 - y1) - (y1 - y2) * (x1 - x3))
            x = x3 + s * (y1 - y2)
            y = y3 + s * (x2 - x1)

            sb.append(String.format("%.4f %.4f\n", x, y))
        }

        print(sb)
    }
}

fun main() {
    BOJ_2773().solve()
}
```