# 24_03_30_daily_certification

```
[koreii] #90 데일리인증
알고리즘 & 코딩 테스트 대비
- 구현, 덱 문제풀이
```

# Problem Solving (Algorithm & SQL)

**BOJ 23300 웹 브라우저 2**

[20917번: 사회적 거리 두기](https://www.acmicpc.net/problem/20917)

이전에 23294 웹 브라우저 1을 해결했으면 오히려 조건이 더 적어져서 쉽다.

[23294번: 웹 브라우저 1](https://www.acmicpc.net/problem/23294)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_23300 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  웹 페이지 종류 수
    private var q = 0   //  작업 수

    private var curPage = -1    //  현재 페이지
    private var backwardDeque = LinkedList<Int>()   //  뒤로 가기 공간
    private val forwardDeque = LinkedList<Int>()    //  앞으로 가기 공간

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        q = tokens.nextToken().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())

            val cmd = tokens.nextToken()[0]

            when(cmd) {
                'B' -> goBackward()
                'F' -> goForward()
                'A' -> {
                    val pageNum = tokens.nextToken().toInt()
                    accessPage(pageNum)
                }
                else -> compress()
            }
        }

        printAns()
    }

    private fun goBackward() {
        if(backwardDeque.isNotEmpty()) {
            forwardDeque.offerLast(curPage)
            curPage = backwardDeque.pollLast()
        }
    }

    private fun goForward() {
        if(forwardDeque.isNotEmpty()) {
            backwardDeque.offerLast(curPage)
            curPage = forwardDeque.pollLast()
        }
    }

    private fun accessPage(pageNum : Int) {
        forwardDeque.clear()

        if(curPage != -1)
            backwardDeque.offerLast(curPage)
        curPage = pageNum
    }

    private fun compress() {
        val tmp = LinkedList<Int>()

        while(backwardDeque.isNotEmpty()) {
            val out = backwardDeque.pollFirst()

            if(tmp.isEmpty() || tmp.peekLast() != out)
                tmp.offerLast(out)
        }

        backwardDeque = tmp
    }

    private fun printAns() {
        sb.append("$curPage\n")
        if(backwardDeque.isNotEmpty()) {
            while(backwardDeque.isNotEmpty())
                sb.append("${backwardDeque.pollLast()} ")
        } else sb.append(-1)
        sb.append("\n")
        if(forwardDeque.isNotEmpty()) {
            while(forwardDeque.isNotEmpty())
                sb.append("${forwardDeque.pollLast()} ")
        } else sb.append(-1)
        sb.append("\n")

        print(sb)
    }
}

fun main() {
    BOJ_23300().solve()
}
```