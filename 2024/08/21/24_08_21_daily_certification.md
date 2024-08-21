# 24_08_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6191 **Cows on Skates**

[](https://www.acmicpc.net/problem/6191)

```kotlin
import java.util.*
import java.io.*

class BOJ_6191 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var st: StringTokenizer

    private lateinit var prev : Array<Array<IntArray>>  //  prev[y][x] : (y, x)칸의 이전 칸 {py, px}
    private lateinit var map : Array<CharArray>
    private lateinit var visited : Array<BooleanArray>

    private val stack = Stack<IntArray>()

    private var n = 0
    private var m = 0

    fun solve() {
        st = StringTokenizer(br.readLine())
        n = st.nextToken().toInt()
        m = st.nextToken().toInt()

        prev = Array(n) { Array(m) { IntArray(2) } }
        map = Array(n) { CharArray(m) }
        visited = Array(n) { BooleanArray(m) }

        for(r in 0 ..< n)
            map[r] = br.readLine().toCharArray()

        bfs()

        var cy = n - 1
        var cx = m - 1

        while(cy != -1 && cx != -1) {
            stack.push(intArrayOf(cy + 1, cx + 1))
            val ny = prev[cy][cx][0]
            val nx = prev[cy][cx][1]

            cy = ny
            cx = nx
        }

        while(stack.isNotEmpty()) {
            val cur = stack.pop()

            sb.append("${cur[0]} ${cur[1]}\n")
        }

        print(sb)
    }

    private fun bfs() {
        val q = LinkedList<IntArray>()
        visited[0][0] = true
        prev[0][0][0] = -1
        prev[0][0][0] = -1

        q.offer(intArrayOf(0, 0))

        while(q.isNotEmpty()) {
            val cur = q.poll()
            val cy = cur[0]
            val cx = cur[1]

            for(d in 0 ..< 4) {
                val ny = cy + dy[d]
                val nx = cx + dx[d]

                if(isIn(ny, nx) && map[ny][nx] != '*' && !visited[ny][nx]) {
                    visited[ny][nx] = true
                    prev[ny][nx][0] = cy
                    prev[ny][nx][1] = cx

                    if(ny == n - 1 && nx == m - 1)
                        return

                    q.offer(intArrayOf(ny, nx))
                }
            }
        }
    }

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< m
    }
}

fun main() {
    BOJ_6191().solve()
}
```