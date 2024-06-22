# 24_06_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11780 **플로이드 2**

[](https://www.acmicpc.net/problem/11780)

```kotlin
import java.util.*
import java.io.*

const val INF = 10_000_000_001
const val NONE = -1

class BOJ_11780 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  정점 수
    private var m = 0   //  단방향 간선 수
    private lateinit var dist : Array<LongArray> //  dist[i][j] : i에서 j로 가는 최단 거리, 도달할 수 없으면 INF
    private lateinit var path : Array<IntArray> //  path[i][j] : i에서 j까지 최단 거리로 갈 때 중간에 있는 정점 중 가장 번호가 큰 정점, i에서 j로 도달 불가하거나, 중간 정점이 없으면 NONE

    fun solve() {
        n = br.readLine().toInt()
        m = br.readLine().toInt()

        dist = Array(n + 1) { LongArray(n + 1 ) { INF } }
        for(i in 0 .. n)
            dist[i][i] = 0

        path = Array(n + 1) { IntArray(n + 1) { NONE } }

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toLong()

            if(cost < dist[v1][v2])
                dist[v1][v2] = cost
        }

        floydWarshall()

        for(i in 1 .. n) {
            for(j in 1 .. n)
                sb.append("${if(dist[i][j] == INF) 0 else dist[i][j]} ")
            sb.append("\n")
        }

        for(from in 1 .. n) {
            for(to in 1 .. n)
                printPath(from, to)
        }

        print(sb)
    }

    private fun floydWarshall() {
        for(k in 1 .. n) {
            for(i in 1 .. n) {
                for(j in 1 .. n) {
                    if(dist[i][j] > dist[i][k] + dist[k][j]) {  //  기존 i에서 j로 가는 최단 거리보다 i에서 k를 거치고, k에서 j로 가는 최단 거리가 더 짧으면
                        path[i][j] = k  //  i에서 j로 가는 최단 경로의 중간 정점 중 가장 큰 정점이 path[i][j]
                        dist[i][j] = dist[i][k] + dist[k][j]    //  i에서 j로 가는 최단 거리 갱신
                    }
                }
            }
        }
    }

    private val pathList = ArrayList<Int>()

    //  from부터 to까지 최단 경로에 포함된 정점 개수를 출력하고 (from, to 포함)
    //  from부터 to까지 경로 출력
    private fun printPath(from : Int, to : Int) {
        if (dist[from][to] == INF || from == to) { //  from에서 to로 갈 수 없을 경우, 또는 자기 자신으로 가는 경우
            sb.append("0\n")
            return
        }

        pathList.clear()

        sb.append("${path(from, to) + 2} ")
        sb.append("$from ")
        for(v in pathList)
            sb.append("$v ")
        sb.append("$to\n")
    }

    //  from에서 to까지의 최단 경로 중 중간 정점들을 리스트에 넣고 개수를 반환
    private fun path(from : Int, to : Int) : Int {
        val mid = path[from][to]
        var ret = 0

        if(mid == NONE) //  만약 중간에 어떤 정점도 없으면
            return 0

        ret += path(from, mid)  //  from에서 mid까지의 최단 경로 중 중간 정점들을 리스트에 넣고 개수 반환
        ret++   //  mid 개수 1 추가
        pathList.add(mid)   //  mid를 리스트에 넣음
        ret += path(mid, to)    //  mid에서 to까지의 최단 경로 중 중간 정점들을 리스트에 넣고 개수 반환

        return ret
    }

}

fun main() {
    BOJ_11780().solve()
}
```