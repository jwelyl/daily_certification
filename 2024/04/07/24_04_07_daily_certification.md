# 24_04_07_daily_certification

```
[koreii] #98 데일리인증
알고리즘 & 코딩 테스트 대비
- Greedy, Sorting, Sweeping 문제 풀이
```

# Problem Solving (Algorithm & SQL)

**BOJ 1689 겹치는 선분**

[1689번: 겹치는 선분](https://www.acmicpc.net/problem/1689)

예제 1을 그림으로 나타내면 아래와 같다.

![ex1.jpeg](24_04_07_daily_certification%2092a5e783fc0640d78a60463006b79a54/ex1.jpeg)

각 선분을 (시작점, 끝점) 순으로 오름차순 정렬하면 다음과 같다.

![ex1_1.jpeg](24_04_07_daily_certification%2092a5e783fc0640d78a60463006b79a54/ex1_1.jpeg)

첫 번째 선분부터 우선순위 큐에 넣는데, 선분의 끝 점을 기준으로 작은 값을 우선하는 우선순위 큐에 넣는다.

![ex1_2.jpeg](24_04_07_daily_certification%2092a5e783fc0640d78a60463006b79a54/ex1_2.jpeg)

정렬된 순서대로 선분을 우선순위 큐에 넣는다. 이 때 이미 우선순위 큐에 존재하는 선분들을 확인하여 우선순위 큐에 존재하는 끝점이 우선순위 큐에 넣으려는 선분의 시작점보다 작을 경우, 우선순위 큐의 해당 선분을 제거한다.

우선순위 큐에 들어있는 선분 개수가 겹치는 선분 개수이다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1689 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  선분 개수
    private val lines = ArrayList<Line>()
    private val pq = PriorityQueue<Int>()   //  겹쳐지는 선분의 끝 점
    private var ans = 1 //  최대로 겹쳐지는 선분 개수 (하나의 선분 자체가 1개 겹쳐지는 것)

    fun solve() {
        n = br.readLine().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())

            lines.add(Line(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        lines.sort()

        pq.offer(lines[0].end)

        for(i in 1 ..< n) {
            val start = lines[i].start
            val end = lines[i].end

            while(pq.isNotEmpty() && pq.peek() <= start)
                pq.poll()

            pq.offer(end)

            ans = ans.coerceAtLeast(pq.size)
        }

        println(ans)
    }

    //  선분을 start가 작은 순으로 정렬
    //  start가 같을 경우 end가 작은 순으로 정렬
    private class Line(val start : Int, val end : Int) : Comparable<Line> {
        override fun compareTo(other: Line): Int {
            val ret = this.start.compareTo(other.start)

            return if(ret != 0) ret else this.end.compareTo(other.end)
        }
    }
}

fun main() {
    BOJ_1689().solve()
}
```