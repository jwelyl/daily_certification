# 24_08_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 27440 1로 만들기 3

[](https://www.acmicpc.net/problem/27440)

```kotlin
import java.util.*
import java.io.*

class BOJ_27440 {
    private val br = BufferedReader(InputStreamReader(System.`in`))

    private var n = 0L
    private val visited = HashSet<Long>()

    fun solve() {
        n = br.readLine().toLong()

        println(bfs())
    }

    private fun bfs() : Long {
        if(n == 1L)
            return 0

        val queue = LinkedList<LongArray>()
        visited.add(n)
        queue.offer(longArrayOf(n, 0))

        while(queue.isNotEmpty()) {
            val out = queue.poll()
            val cnum = out[0]
            val ccnt = out[1]

            if(cnum % 3 == 0L) {
                if(cnum / 3 == 1L)
                    return ccnt + 1

                if(!visited.contains(cnum / 3)) {
                    visited.add(cnum / 3)
                    queue.offer(longArrayOf(cnum / 3, ccnt + 1))
                }
            }
            if(cnum % 2 == 0L) {
                if(cnum / 2 == 1L)
                    return ccnt + 1

                if(!visited.contains(cnum / 2)) {
                    visited.add(cnum / 2)
                    queue.offer(longArrayOf(cnum / 2, ccnt + 1))
                }
            }
            if(cnum - 1 == 1L)
                return ccnt + 1

            if(!visited.contains(cnum - 1)) {
                visited.add(cnum - 1)
                queue.offer(longArrayOf(cnum - 1, ccnt + 1))
            }
        }

        return -1L
    }
}

fun main() {
    BOJ_27440().solve()
}
```