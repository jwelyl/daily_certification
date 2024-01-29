# 24_01_29_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1781 컵라면**

[1781번: 컵라면](https://www.acmicpc.net/problem/1781)

문제 리스트를 deadLine이 짧은 문제부터 정렬한다. 문제 리스트의 문제들을 순서대로 확인하면서 문제의 reward를 우선순위 큐에 집어넣는다. 우선순위 큐에 있는 문제 개수는 문제를 푸는 데 소요된 시간이 된다.

우선순위 큐의 size가 현재 문제의 deadLine보다 클 경우, 우선순위 큐에 있는 문제들 중 가장 reward가 작은 문제를 제거한다. 이 과정을 모든 문제에 대해 반복하면 우선 순위 큐에는 풀 수 있는 문제들만 남게 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1781 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private val problemList = ArrayList<Problem>()
    private val pq = PriorityQueue<Int>()

    private var ans = 0

    fun solve() {
        n = br.readLine().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())

            problemList.add(Problem(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        problemList.sortBy{it.deadLine} //  데드라인이 짧은 문제부터

        for(i in 0 ..< n) {
            val problem = problemList[i]    //  i번째로 데드라인 짧은 문제

            pq.offer(problem.reward)    //  데드라인이 짧은 문제부터 보상을 pq에 넣음

            if(problem.deadLine < pq.size)  //  현재 문제의 데드라인을 넘겼을 경우
                pq.poll()   //  pq에 저장된 문제의 보상들 중 가장 보상이 적은 문제를 제거
        }

        while(pq.isNotEmpty())
            ans += pq.poll()

        println(ans)
    }

    private class Problem(val deadLine : Int, val reward : Int)
}

fun main() {
    BOJ_1781().solve()
}
```
