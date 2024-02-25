# 24_02_25_daily_certification

```
[koreii] #56 데일리인증
20240224
알고리즘
- Greedy & Priority Queue 문제 풀이
- 실패한 코드와 성공한 코드 비교 분석하기
```

# Problem Solving (Algorithm & SQL)

**BOJ 23843 콘센트**

[23843번: 콘센트](https://www.acmicpc.net/problem/23843)

가장 충전 시간이 오래 걸리는 것부터 충전시키면 된다고 생각하고 접근했다. (결과적으론 맞지만 왜 그럴까?)

**실패한 접근 방법**

 따라서 충전 시간이 큰 순서로 전자기기를 pq에 저장하였고 pq에서 최대 m개씩 뽑아서 충전에 필요한 시간을 1씩 감소시켜줬다. 충전에 필요한 시간이 0일 경우 다시 pq에 넣지 않고 0보다 클 경우에는 다시 충전해야 하므로 pq에 넣어줬다.

**실패한 코드 (시간 초과)**

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_23843 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  충전할 전자기기 개수
    private var m = 0   //  콘센트 개수
    private val pq = PriorityQueue<Int>{ it1, it2 -> it2 - it1 }    //  충전 시간이 오래 걸리는 순으로 저장
    private val tmp = LinkedList<Int>()import java.util.*
    import java.io.*
    import kotlin.collections.ArrayList

    class BOJ_23843 {
        private val br = BufferedReader(InputStreamReader(System.`in`))
        private val sb = StringBuilder()
        private lateinit var tokens: StringTokenizer

        private var n = 0   //  충전할 전자기기 개수
        private var m = 0   //  콘센트 개수
        private val pq = PriorityQueue<Int>{ it1, it2 -> it2 - it1 }    //  충전 시간이 오래 걸리는 순으로 저장
        private val tmp = LinkedList<Int>() //  1씩 충전시키고 다시 충전시켜야 하는 전자기기

        private var time = 0    //  n개 전자기기 충전을 위해 필요한 최소 시간

        fun solve() {
            tokens = StringTokenizer(br.readLine())
            n = tokens.nextToken().toInt()
            m = tokens.nextToken().toInt()

            tokens = StringTokenizer(br.readLine())
            for(i in 0 ..< n)
                pq.offer(tokens.nextToken().toInt())

            while(pq.isNotEmpty()) {
                for(out in m.coerceAtMost(pq.size) downTo 1) {  //  충전량 큰 것부터 최대 m개만큼 빼서 충전
                    val poll = pq.poll() - 1    //  pq에서 빼서 1만큼 충전

                    if(poll > 0)    //  아직 더 충전해야 할 경우 tmp에 저장
                        tmp.add(poll)
                }

                while(tmp.isNotEmpty()) //  더 충전해야 할 것들 다시 pq에 저장
                    pq.offer(tmp.poll())

                time++
            }

            println(time)
        }
    }

    fun main() {
        BOJ_23843().solve()
    }

    private var time = 0    //  n개 전자기기 충전을 위해 필요한 최소 시간

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            pq.offer(tokens.nextToken().toInt())

        while(pq.isNotEmpty()) {
            for(out in m.coerceAtMost(pq.size) downTo 1) {
                val poll = pq.poll() - 1    //  pq에서 빼서 1만큼 충전

                if(poll > 0)
                    tmp.add(poll)
            }

            while(tmp.isNotEmpty())
                pq.offer(tmp.poll())

            time++
        }

        println(time)
    }
}

fun main() {
    BOJ_23843().solve()
}
```

시간 초과가 발생하는 가장 주요한 이유를 생각해보면 시간을 1씩 줄일 경우 전자기기 하나에 대해서만 최대 2^15번 pq에서 빼고 넣는 작업을 해야 한다.

시간초과를 제외하고 생각해보더라도 문제에서 요구하는 내용과 구현이 일치하지 않는다. 

m개의 전자기기를 충전시킬 수 있는 콘센트를 추가로 구현한다. 충전이 완료될 경우 제거하는 식으로 구현해야하므로 기존에 충전시간이 큰 순으로 저장한 pq1 외에도 충전시간이 작은 순으로 pq2를 만들어서 구현한다.

최대 m개의 전자기기를 pq1에서 제거해서 pq2에 저장하고 pq2에 있는 전자기기 중 가장 충전 시간이 짧은 것의 시간만큼 pq2에 있는 전자기기의 충전시간을 감소시킨다.

### 성공한 코드

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_23843 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  충전할 전자기기 개수
    private var m = 0   //  콘센트 개수
    private val pq1 = PriorityQueue<Int>{ it1, it2 -> it2 - it1 }    //  충전 시간이 오래 걸리는 순으로 저장
    private val pq2 = PriorityQueue<Int>()  //  콘센트, 필요 충전량이 적은 순서대로 저장
    private val q = LinkedList<Int>()   //  충전시키고 마저 충전시켜야 하는 전자기기
    private var time = 0    //  n개 전자기기 충전을 위해 필요한 최소 시간

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            pq1.offer(tokens.nextToken().toInt())

        //  일단 충전 오래 걸리는 m개 먼저 충전 시작
        var inSize = m.coerceAtMost(pq1.size)   //  최대 m개 충전 가능
        for(i in 0 ..< inSize)
            pq2.offer(pq1.poll())

        while(pq2.isNotEmpty()) {
            val minTime = pq2.peek()   //  가장 충전 빨리 끝나는 전자기기 충전 시간
            val outSize = m.coerceAtMost(pq2.size)

            for(i in 0 ..< outSize) {
                if(pq2.peek() - minTime == 0)   //  minTime만 지나면 다 충전될 경우
                    pq2.poll()      //  충전 다 시켰으니 콘센트에서 제거
                else
                    q.offer(pq2.poll() - minTime) //  minTime만큼 충전시켜서 다시 넣기
            }

            while(q.isNotEmpty())
                pq2.offer(q.poll())

            time += minTime

            inSize = (m - pq2.size).coerceAtMost(pq1.size)  //  pq1에 남아있는 전자기기 마저 충전시키기
            for(i in 0 ..< inSize)
                pq2.offer(pq1.poll())
        }

        println(time)
    }
}

fun main() {
    BOJ_23843().solve()
}
```