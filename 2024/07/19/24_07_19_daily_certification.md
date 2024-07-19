# 24_07_19_daily_certification

# Operating System

# Problem Solving (Algorithm & SQL)

### BOJ 17430 가로등

[](https://www.acmicpc.net/problem/17430)

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_17430 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val map = HashMap<Int, HashSet<Int>>()
    private var tc = 0
    private var n = 0

    fun solve() {
        tc = br.readLine().toInt()

        repeat(tc) {
            map.clear()

            n = br.readLine().toInt()
            var balanced = true

            repeat(n) {
                tokens  = StringTokenizer(br.readLine())
                val x = tokens.nextToken().toInt()
                val y = tokens.nextToken().toInt()

                if(!map.containsKey(x))
                    map[x] = HashSet<Int>()
                map[x]!!.add(y)
            }

            val entrySet = map.entries
            var before : HashSet<Int>? = null

            for(entry in entrySet) {
                val value = entry.value

                if(before == null)
                    before = value
                else if(before != value) {
                    if(!(before.containsAll(value) && value.containsAll(before))) {
                        balanced = false
                        break
                    }
                }
            }

            sb.append(if(balanced) "BALANCED\n" else "NOT BALANCED\n")
        }

        print(sb)
    }

    private fun toString(list : ArrayList<Int>) : String {
        val ret = StringBuilder()
        list.sort()

        for(num in list)
            ret.append(num).append("@")

        return ret.toString()
    }
}

fun main() {
    BOJ_17430().solve()
}
```