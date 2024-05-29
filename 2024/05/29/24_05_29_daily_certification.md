# 24_05_29_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 삼오무**     

[https://www.codetree.ai/missions/8/problems/three-five-moo/description](https://www.codetree.ai/missions/8/problems/three-five-moo/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/three-five-moo/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))

    private var n = 0

    fun solve() {
        n = br.readLine().toInt()

        println(parametricSearch())
    }

    private fun parametricSearch() : Int {
        var start = 1
        var end = Int.MAX_VALUE

        var ret = Int.MAX_VALUE

        while(start <= end) {
            val mid = start + (end - start) / 2
            val cnt = cnt(mid)

           if(cnt >= n) {
               end = mid - 1
               ret = minOf(ret, mid)
           }
            else start = mid + 1
        }

        return ret
    }

    private fun cnt(num : Int) : Int {
        return num - num / 3 - num / 5 + num / 15
    }
}

fun main() {
    Main().solve()
}
```