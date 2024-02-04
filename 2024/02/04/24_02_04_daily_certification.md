# 24_02_04_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 13202 피자 배치**

[13202번: 피자 배치](https://www.acmicpc.net/problem/13202)

직각삼각형의 내접원의 중심에서 삼각형의 세 꼭짓점을 향한 원의 닮음비를 각각 x, y, z라 하고 이를 구하면 되는 문제이다.

x, y, z를 구하는 과정은 그림으로 대체한다.

![1.jpeg](24_02_04_daily_certification%2038aa934f15d24ab18e2f07f029eb19cf/1.jpeg)

![2.jpeg](24_02_04_daily_certification%2038aa934f15d24ab18e2f07f029eb19cf/2.jpeg)

![4.jpeg](24_02_04_daily_certification%2038aa934f15d24ab18e2f07f029eb19cf/4.jpeg)

![3.jpeg](24_02_04_daily_certification%2038aa934f15d24ab18e2f07f029eb19cf/3.jpeg)

주의 사항으로는 x, y, z의 크기 순서를 알더라도 그로 인해 만들어지는 원의 넓이 순서를 확신할 수 없다. 아래 그림을 보면 알겠지만 원이 크기 순으로 생기는 순서를 알 수 없다.

![pizza.png](24_02_04_daily_certification%2038aa934f15d24ab18e2f07f029eb19cf/pizza.png)

따라서 각 방향으로 100개씩(k가 최대 100이므로) 닮음비를 리스트에 저장하고 이를 내림차순으로 정렬하여 100번째로 큰 닮음비를 가져다가 원의 넓이를 구하면 된다.

1

x, x^2, x^3, …, x^100

y, y^2, y^3, …, y^100

z, z^2, z^3, …, z^100

이 중 100번째로 큰 닮음비를 가져오면 된다.

재미있는 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.*

class BOJ_13202 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var t = 0
    private var a = 0.0
    private var b = 0.0
    private var k = 0

    private var c = 0.0 //  빗변 길이
    private var r = 0.0 //  내접하는 가장 큰 원 반지름
    private var x = 0.0 //  닮음비 1
    private var y = 0.0 //  닮음비 2
    private var z = 0.0 //  닮음비 3
    private val ratioList = ArrayList<Double>() //  닮음비 저장 리스트

    fun solve() {
        t = br.readLine().toInt()

        repeat(t) {
            tokens = StringTokenizer(br.readLine())
            a = tokens.nextToken().toDouble()
            b = tokens.nextToken().toDouble()
            k = tokens.nextToken().toInt()

            ratioList.clear()
            ratioList.add(1.0)

            c = sqrt(a * a + b * b)
            r = a * b / (a + b + c)

            x = (sqrt((a - r) * (a - r) + r * r) - r) / (sqrt((a - r) * (a - r) + r * r) + r)
            y = (sqrt((b - r) * (b - r) + r * r) - r) / (sqrt((b - r) * (b - r) + r * r) + r)
            z = (sqrt(2.0) - 1.0) / (sqrt(2.0) + 1.0)

            var r1 = x
            var r2 = y
            var r3 = z

            for(i in 1 .. 100) {
                ratioList.add(r1)
                ratioList.add(r2)
                ratioList.add(r3)

                r1 *= x
                r2 *= y
                r3 *= z
            }

            ratioList.sortByDescending { it }

            val ratio = ratioList[k - 1]

            sb.append("${PI * r * r * ratio * ratio}\n")
        }

        print(sb)
    }
}

fun main() {
    BOJ_13202().solve()
}
```