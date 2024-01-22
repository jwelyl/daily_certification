# 24_01_23_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 5547 일루미네이션**

[5547번: 일루미네이션](https://www.acmicpc.net/problem/5547)

가장자리를 빈 칸으로 채우고 (0, 0)에서부터 DFS 또는 BFS로 탐색하여 외부에 속한 빈 칸과 내부에 속한 빈 칸을 구분한다.

빈 칸이 아닌 칸 각각에 대하여 외부와 맞닿은 면의 개수를 구해서 더해주면 된다.

주의할 점은 각 칸의 인접한 칸에 대한 정의가 문제에 주어져 있다. 문제 조건을 보는게 아니라 임의로 그림을 보고 해석해서 정답을 도출하는데 오래 걸렸다. 또한 y 좌표에 따라 dy, dx가 변하므로 이를 고려해야 했다.

마지막으로 dfs로 외부, 내부를 구분지을 때도 통상적인 4방향 탐색이 아니라 문제에 주어진 dy, dx로 탐색해야 했다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_5547 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var w = 0
    private var h = 0

    private lateinit var map : Array<IntArray>
    private lateinit var visited : Array<BooleanArray>

    private var ans = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        w = tokens.nextToken().toInt()
        h = tokens.nextToken().toInt()

        map = Array(h + 2) { IntArray(w + 2) }
        visited = Array(h + 2) { BooleanArray(w + 2) }

        for(i in 1 .. h) {
            tokens = StringTokenizer(br.readLine())
            for(j in 1 .. w)
                map[i][j] = tokens.nextToken().toInt()
        }

        dfs(0, 0)

        for(i in 1 .. h) {
            for(j in 1 .. w) {
                if(map[i][j] == 1) {
                    var cnt = 0

                    val dir = i % 2

                    for(d in dy[dir].indices) {
                        val y = i + dy[dir][d]
                        val x = j + dx[dir][d]

                        if(visited[y][x])
                            cnt++
                    }

                    ans += cnt
                }
            }
        }

        println(ans)
    }

    private fun dfs(y : Int, x : Int) {
        visited[y][x] = true

        val dir = y % 2

        for(d in dy[dir].indices) {
            val ny = y + dy[dir][d]
            val nx = x + dx[dir][d]

            if(isIn(ny, nx) && map[ny][nx] == 0 && !visited[ny][nx])
                dfs(ny, nx)
        }
    }

    private val dy = arrayOf(intArrayOf(0, 1, 0, -1, 1, -1), intArrayOf(0, 1, 1, 0, -1, -1))
    private val dx = arrayOf(intArrayOf(1, 0, -1, 0, -1, -1), intArrayOf(1, 1, 0, -1, 0, 1))

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 .. h + 1 && x in 0 .. w + 1
    }
}

fun main() {
    BOJ_5547().solve()
}
```