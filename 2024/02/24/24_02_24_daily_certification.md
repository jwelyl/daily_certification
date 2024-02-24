# 24_02_24_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1486 등산**

[1486번: 등산](https://www.acmicpc.net/problem/1486)

시작점 0에서 임의의 정점 v까지 간 후, v에서 다시 시작점 0까지 돌아오는 최소 시간을 구해야 한다. 왕복에만 주목하면 어떻게 해결해야 할 지 막막하지만, 0→v까지의 최소 시간, v→0까지의 최소 시간을 구하고 이를 더하면 0, v를 왕복하는 시간을 구할 수 있다. 즉 Dijkstra를 2번 적용하면 된다.

일단 주어진 각 격자의 높이를 이용해 인접 그래프를 모델링해야 한다.

![Untitled](24_02_24_daily_certification%20be566daca1294d74907698c8ae3aaf67/Untitled.png)

n \* m 격자에서 좌측 상단을 0, 우측 하단을 nm - 1이라고 모델링하면 (y, x)는 my + x가 된다.

반대로 V 정점은 격자 상 (V / m, V % m)에 위치하게 된다.

**0→V 최소 시간**

그래프를 모델링한 후 0에서 임의의 v까지는 한 번의 Dijkstra로 최소 시간을 구할 수 있다.

**V → 0 최소 시간**

반대로 임의의 v에서 0까지의 최소 시간은 주어진 그래프가 양방향 그래프가 아니라면 그대로 활용해서는 한 번의 Dijkstra로 구할 수 없다.

역방향 그래프에서 정점 0에서 임의의 v까지의 최소 거리는 원래 주어진 정방향 그래프에서 v에서 0까지의 최소 거리이다. 따라서 역방향 그래프를 모델링한 후 역방향 그래프에 한 번의 Dijkstra를 적용하면 된다.

따라서 총 2번의 Dijkstra로 0과 임의의 v의 왕복에 필요한 최소 시간을 구할 수 있다.

![Untitled](24_02_24_daily_certification%20be566daca1294d74907698c8ae3aaf67/Untitled%201.png)

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList
import kotlin.math.abs

class BOJ_1486 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    private var n = 0
    private var m = 0
    private var t = 0   //  허용된 높이 차이
    private var d = 0   //  제한 시간

    private lateinit var mount : Array<CharArray>

    private lateinit var fGraph : Array<ArrayList<Node>>   //  정방향 그래프
    private lateinit var rGraph : Array<ArrayList<Node>>   //  역방향 그래프
    private lateinit var fTime : IntArray           //  fDist[i] : 정방향 그래프에서 0에서 i까지의 최소 시간
    private lateinit var rTime : IntArray           //  rDist[i] : 역방향 그래프에서 0에서 i까지의 최소 시간(정방향 그래프에서 i에서 0까지의 최소 시간)

    private var maxHeight = Int.MIN_VALUE   //  d 안에 갔다 올 수 있는 최대 높이

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        t = tokens.nextToken().toInt()
        d = tokens.nextToken().toInt()

        mount = Array(n) { CharArray(m) }

        fGraph = Array(n * m) { ArrayList() }
        rGraph = Array(n * m) { ArrayList() }
        fTime = IntArray(n * m) { Int.MAX_VALUE }
        rTime = IntArray(n * m) { Int.MAX_VALUE }

        for(i in 0 ..< n)
            mount[i] = br.readLine().toCharArray()

        for(cy in 0 ..< n) {
            for(cx in 0 ..< m) {
                val cV = m * cy + cx
                val cH = height(mount[cy][cx])

                for(d in 0 ..< 4) {
                    val ny = cy + dy[d]
                    val nx = cx + dx[d]

                    if(isIn(ny, nx)) {
                        val nV = m * ny + nx
                        val nH = height(mount[ny][nx])

                        if(abs(nH - cH) <= t) { //  높이 차이가 허용 범위일 경우
                            if(cH > nH) {   //  다음 위치 높이가 더 낮을 경우
                                fGraph[cV].add(Node(nV, 1))
                                rGraph[cV].add(Node(nV, (cH - nH) * (cH - nH)))
                            }
                            else if(cH == nH) { // 두 위치 높이가 같을 경우
                                fGraph[cV].add(Node(nV, 1))
                                rGraph[cV].add(Node(nV, 1))
                            }
                            else {  //  다음 위치 높이가 더 높을 경우
                                fGraph[cV].add(Node(nV, (cH - nH) * (cH - nH)))
                                rGraph[cV].add(Node(nV, 1))
                            }
                        }
                    }
                }
            }
        }

        dijkstra(fGraph, fTime)
        dijkstra(rGraph, rTime)

        for(i in 0 ..< n * m) {
            val y = i / m
            val x = i % m

            //  i 지점까지 갈 수 없을 경우
            if(fTime[i] == Int.MAX_VALUE || rTime[i] == Int.MAX_VALUE)
                continue

            if(fTime[i] + rTime[i] <= d)  //  0에서 i까지 d 시간 안에 갔다 올 수 있을 경우
                maxHeight = maxHeight.coerceAtLeast(height(mount[y][x]))
        }

        println(maxHeight)
    }

    private fun dijkstra(graph : Array<ArrayList<Node>>, time : IntArray) {
        val pq = PriorityQueue<Node>()
        time[0] = 0
        pq.offer(Node(0, time[0]))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val cV = cur.v
            val cCost = cur.cost

            if(time[cV] < cCost)
                continue

            for(next in graph[cV]) {
                val nV = next.v
                val nCost = cCost + next.cost

                if(time[nV] > nCost) {
                    time[nV] = nCost
                    pq.offer(Node(nV, time[nV]))
                }
            }
        }

    }

    private fun height(ch : Char) : Int {
        return if(ch in 'A' .. 'Z') ch.code - 'A'.code else ch.code - 'a'.code + 26
    }

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< m
    }

    private class Node(val v : Int, val cost : Int) : Comparable<Node> {
        override fun compareTo(other: Node): Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    BOJ_1486().solve()
}
```
