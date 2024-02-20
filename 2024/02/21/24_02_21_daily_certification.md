# 24_02_21_daily_certification

```
[koreii] #52 데일리인증
20240221
알고리즘
- floyd-warshall 알고리즘 문제 풀이
- 무엇을 최소화할 것인가를 파악하고 최단경로 알고리즘 적용시키기
```

# Problem Solving (Algorithm & SQL)

**BOJ 11562 백양로 브레이크**

[11562번: 백양로 브레이크](https://www.acmicpc.net/problem/11562)

n이 최대 250이므로 플로이드 워셜을 이용해 O(n^3)에도 충분히 해결 가능한 문제다.

처음부터 최단 경로 문제를 찾다가 이 문제를 발견한 것이므로 플로이드 워셜을 이용하겠다는 것 자체는 금방 찾을 수 있었지만, 이전에 풀었던 플로이드 워셜 문제처럼 무작정 두 정점 사이의 거리를 최소화해야 한다는 생각으로 접근해서는 별로 얻을 수 있는 게 없다.

문제에서 요구하는 것이 임의의 s에서 임의의 t로 갈 때 양방향 통행으로 바꿔야 하는 길의 최소 개수를 구하는 것이므로 두 정점 사이의 거리가 아니라 두 정점 사이에 양방향 통행으로 바꿔야 하는 길의 개수를 놓고 플로이드 워셜을 적용해야 했다. 이 생각을 가지고 있었다면 그래프를 구축하는 idea도 얻을 개연성이 높아졌을 것 같다.

어쨋든 위의 idea를 적용하면 다음과 같이 그래프를 구축할 수 있다.

![floyd1.jpeg](24_02_21_daily_certification%20940fbc613e1a417eb343f2625cf9ce6e/floyd1.jpeg)

플로이드 워셜을 적용하면 임의의 s에서 임의의 t로 갈 때 양방향 통행으로 바꿔야 하는 길의 최소 개수를 구할 수 있다.

![floyd2.jpeg](24_02_21_daily_certification%20940fbc613e1a417eb343f2625cf9ce6e/floyd2.jpeg)

**코드**

```kotlin
import java.util.*
import java.io.*

const val INF = 250 * 250

class BOJ_11562 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var m = 0

    //  dp[i][j] : i에서 j로 가기 위해 일방 통행에서 양방향 통행으로 뒤집어야 하는 최소 횟수
    private lateinit var dp : Array<IntArray>

    private var k = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        dp = Array(n + 1) { IntArray(n + 1) { INF } }
        for(i in 1 .. n)
            dp[i][i] = 0

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val u = tokens.nextToken().toInt()
            val v = tokens.nextToken().toInt()
            val b = tokens.nextToken().toInt()

            dp[u][v] = 0    //  u에서 v는 그냥 갈 수 있음
            dp[v][u] = if(b == 1) 0 else 1  //  양방향 통행일 경우 v에서 u도 그냥 갈 수 있고, 아니면 양방향 통행으로 1번 바꿔야 갈 수 ㅣㅆ음
        }

        floydWarshall()

        k = br.readLine().toInt()

        repeat(k) {
            tokens = StringTokenizer(br.readLine())
            val from = tokens.nextToken().toInt()
            val to = tokens.nextToken().toInt()

            sb.append("${dp[from][to]}\n")
        }

        print(sb)
    }

    private fun floydWarshall() {
        for(k in 1 .. n) {
            for(i in 1 .. n) {
                for(j in 1 .. n)
                    dp[i][j] = dp[i][j].coerceAtMost(dp[i][k] + dp[k][j])
            }
        }
    }
}

fun main() {
    BOJ_11562().solve()
}
```