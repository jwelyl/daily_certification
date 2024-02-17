# 24_02_17_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 30108 교육적인 트리 문제**

[30108번: 교육적인 트리 문제](https://www.acmicpc.net/problem/30108)

예제를 잘 보면 결국 Heap Sort로 내림차순으로 정렬하면 된다는 문제라는 것을 알 수 있다.

![1.jpeg](24_02_17_daily_certification%20a4f63be8055448eaa2b8858ad8aea115/1.jpeg)

부모의 값이 결국 자식의 값보다 크기 때문에 최대 힙 구조의 트리가 되는 것을 알 수 있다.

![2.jpeg](24_02_17_daily_certification%20a4f63be8055448eaa2b8858ad8aea115/2.jpeg)

주어진 원소들을 PriorityQueue에 일일이 넣고 다시 하나씩 제거하면서 풀어도 풀리긴 하지만, 직접 힙 자료구조를 만들고 Heapify를 이용하여 힙을 초기화하면 좀 더 시간이 빨라지지 않을까 싶다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_30108 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private val pq = PriorityQueue<Int>()
    private lateinit var parents : IntArray
    private var ans = 0L

    fun solve() {
        n = br.readLine().toInt()
        parents = IntArray(n + 1)

        tokens = StringTokenizer(br.readLine())
        for(i in 2 .. n)
            parents[i] = tokens.nextToken().toInt()

        tokens = StringTokenizer(br.readLine())
        repeat(n) {
            pq.offer(-tokens.nextToken().toInt())
        }

        while(pq.isNotEmpty()) {
            ans += (-pq.poll())
            sb.append("$ans\n")
        }

        print(sb)
    }
}

fun main() {
    BOJ_30108().solve()
}
```

정렬 기준을 최대 힙으로 따로 정하는 것보다 기본적인 minHeap의 PriorityQueue를 쓰고 값을 넣고 뺄 때 부호를 바꿔주면서 사용하면 더 빠른 것 같다.