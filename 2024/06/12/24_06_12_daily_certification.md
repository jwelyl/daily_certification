# 24_06_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22255 호석사우루스

[](https://www.acmicpc.net/problem/22255)

```kotlin
import java.util.*
import java.io.*

const val INF = Int.MAX_VALUE

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0
    private var sy = 0
    private var sx = 0
    private var ey = 0
    private var ex = 0

    private lateinit var map : Array<IntArray>

    //  dist[mod][y][x] : (y, x)까지 3 * k + mod번째에 도달했을 때의 최소 충격량 (mod = 0, 1, 2)
    private lateinit var dist : Array<Array<IntArray>>

    private val dy = arrayOf(intArrayOf(0, 1, 0, -1), intArrayOf(1, -1), intArrayOf(0, 0))
    private val dx = arrayOf(intArrayOf(1, 0, -1, 0), intArrayOf(0, 0), intArrayOf(1, -1))

    private var ans = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        map = Array(n) { IntArray(m) }
        dist = Array(3) { Array(n) { IntArray(m)  { INF } } }

        tokens = StringTokenizer(br.readLine())
        sy = tokens.nextToken().toInt() - 1
        sx = tokens.nextToken().toInt() - 1
        ey = tokens.nextToken().toInt() - 1
        ex = tokens.nextToken().toInt() - 1

        for(i in 0 ..< n) {
            tokens = StringTokenizer(br.readLine())
            for(j in 0 ..< m)
                map[i][j] = tokens.nextToken().toInt()
        }

        dijkstra()

        ans = minOf(dist[0][ey][ex], dist[1][ey][ex], dist[2][ey][ex])

        println(if(ans == INF) -1 else ans)
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()

        dist[0][sy][sx] = 0 //  (sy, sx)에는 mod = 0에 0의 충격량으로 도착
        pq.offer(Node(sy, sx, dist[0][sy][sx], 0))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val cy = cur.y
            val cx = cur.x
            val ccost = cur.cost
            val ccnt = cur.cnt

            if(dist[ccnt][cy][cx] < ccost)  //  (cy, cx)까지 mod = ccnt로 온 것 중 기존에 가장 적은 충격량보다 현재 충격량이 큰 경우 skip
                continue

            val ncnt = (ccnt + 1) % 3   //  (cy, cx)에서 다음 칸으로의 이동은 mod = (ccnt + 1) % 3
            for(d in 0 ..< dy[ncnt].size) {
                val ny = cy + dy[ncnt][d]
                val nx = cx + dx[ncnt][d]

                if(canGo(ny, nx)) { //  다음 칸이 갈 수 있는 칸이면
                    val ncost = ccost + map[ny][nx] //  (ny, nx)까지 mod = ncnt로 ncost에 올 수 있음

                    if(ncost < dist[ncnt][ny][nx]) {    //  (ny, nx)까지 mod = ncnt로 ncost에 올 수 있음
                        dist[ncnt][ny][nx] = ncost
                        pq.offer(Node(ny, nx, ncost, ncnt))
                    }
                }
            }
        }
    }

    private fun canGo(y : Int, x : Int) : Boolean {
        return (y in 0 ..< n) && (x in 0 ..< m) && (map[y][x] != -1)
    }

    private class Node(val y : Int, val x : Int, val cost : Int, val cnt : Int) : Comparable<Node> {
        override fun compareTo(other : Node) : Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    Main().solve()
}
```