# 24_03_11_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1027 고층 건물**

[1027번: 고층 건물](https://www.acmicpc.net/problem/1027)

빌딩 수가 최대 50개이므로, 각 빌딩마다 볼 수 있는 건물 수를 O(N)의 시간 복잡도에 파악 가능하고, 모든 빌딩에 대해서 계산해보면 되므로 O(N^2)의 시간 복잡도로 해결이 가능하다.

예제 1을 그림으로 나타내면 다음과 같다.

![ex1.jpeg](24_03_11_daily_certification%20d84def93bfac4515a17a281330977c66/ex1.jpeg)

1번 건물에서 오른쪽 건물들을 보면 2번 건물에 의해 나머지 건물들은 가로막혀 볼 수 없다. 2번 건물과 1번 건물의 기울기가 4이고, 그 이후 건물과의 기울기는 4보다 작기 때문이다.

![ex1_1.jpeg](24_03_11_daily_certification%20d84def93bfac4515a17a281330977c66/ex1_1.jpeg)

8번 건물에서 왼쪽 건물들을 보면 7번 건물과의 기울기는 -4, 6번 건물과의 기울기는 -3/2, 5번 건물과의 기울기는 0으로 볼 수 있지만 더 왼쪽에 있는 건물들과의 기울기는 5번 건물과의 기울기보다 작기 때문에 더 왼쪽에 있는 건물들은 5번 건물에 의해 막혀서 보이지 않는다.

**왼쪽에 있는 건물들과의 기울기를 구할 때는 기울기 공식으로 기울기를 구한 후 부호를 바꿔준다.**

8번 건물에서 오른쪽 건물들을 보면 9번 건물과의 기울기는 -2이다. 10번 건물과의 기울기도 -2인데, 10번 건물은 9번 건물에 가려져서 보이지 않는다. 11번 건물과의 기울기는 -1/3이고, 12번 건물과의 기울기는 1/4이므로 보인다.

![ex1_2.jpeg](24_03_11_daily_certification%20d84def93bfac4515a17a281330977c66/ex1_2.jpeg)

위와 같은 방식으로 모든 건물에 대해 볼 수 있는 건물의 수를 세보면, 5번 건물에서 7개로 가장 많다.

![ex1_3.jpeg](24_03_11_daily_certification%20d84def93bfac4515a17a281330977c66/ex1_3.jpeg)

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
