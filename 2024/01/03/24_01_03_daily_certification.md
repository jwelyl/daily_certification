# 24_01_03_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 12763 지각하면 안 돼**

[12763번: 지각하면 안 돼](https://www.acmicpc.net/problem/12763)

다익스트라 알고리즘 or 깊이 우선 탐색

1번 건물에서 출발하여 T 이하의 시간으로 N번 건물에 도착할 때 비용의 최솟값을 구해야 한다.

times[i][j]를 1번 건물에서 i 건물까지 j의 비용으로 갈 때의 시간으로 놓고 최소 시간을 구하기 위해 다익스트라 알고리즘을 사용할 수 있다. 다익스트라 알고리즘을 사용할 때 비용이 M을 넘어갈 경우의 예외 처리가 필요하다.

times[N]을 확인하여 T 이하의 시간에 도착할 수 있는 최소 비용 i를 정답으로 하면 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_12763 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  건물 개수
    private var t = 0   //  출석까지 남은 시간
    private var m = 0   //  현재 쓸 수 있는 돈
    private var l = 0   //  길의 개수

    private lateinit var graph : Array<MutableList<Node>>   //  그래프
    private lateinit var times : Array<IntArray> // times[i][j] : j의 돈으로 1에서 i까지 가는데 걸리는 최소 시간

    fun solve() {
        n = br.readLine().toInt()
        tokens = StringTokenizer(br.readLine())

        t = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        graph = Array(n + 1) { mutableListOf() }
        times = Array(n + 1) { IntArray(m + 1) { Int.MAX_VALUE } }

        l = br.readLine().toInt()

        repeat(l) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val time = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toInt()

            graph[v1].add(Node(v2, time, cost))
            graph[v2].add(Node(v1, time, cost))
        }

        dijkstra()

        var ans = -1
        for(i in 0 .. m) {
            if(times[n][i] <= t) {
                ans = i
                break
            }
        }

        println(ans)
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()
        times[1][0] = 0
        pq.offer(Node(1, 0, 0))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val curV = cur.v
            val curTime = cur.time
            val curCost = cur.cost

            if(times[curV][curCost] < curTime)
                continue

            for(next in graph[curV]) {
                val nextV = next.v
                val nextTime = curTime + next.time
                val nextCost = curCost + next.cost

                if(nextCost <= m && times[nextV][nextCost] > nextTime) {
                    times[nextV][nextCost] = nextTime
                    pq.offer(Node(nextV, times[nextV][nextCost], nextCost))
                }
            }
        }
    }

    private class Node(val v : Int, val time : Int, val cost : Int) : Comparable<Node> {
        override fun compareTo(other: Node): Int {
            return this.time.compareTo(other.time)
        }
    }
}

fun main() {
    BOJ_12763().solve()
}
```