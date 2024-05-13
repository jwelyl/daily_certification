# 24_05_13_daily_certification

```
[koreii] #134 데일리인증
2024513
알고리즘 & 코딩 테스트 대비
- 코드트리 PriorityQueue, 중앙값 구하기
```

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 중앙값**

[https://www.codetree.ai/missions/8/problems/median/description](https://www.codetree.ai/missions/8/problems/median/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/median/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    private val sb = StringBuilder()

    private var tc = 0  //  테스트 케이스 개수
    private var m = 0   //  수열 크기

    private val maxHeap = PriorityQueue<Int>(reverseOrder())  //  최대 힙
    private val minHeap = PriorityQueue<Int>()                //  최소 힙
    
    fun solve() {
        tc = br.readLine().toInt()

        repeat(tc) {
            maxHeap.clear()
            minHeap.clear()     //  중앙값 힙 초기화

            m = br.readLine().toInt()

            tokens = StringTokenizer(br.readLine())

            repeat(m) {
                val num = tokens.nextToken().toInt()

                //  최대 힙에 저장된 수의 개수가 최소 힙에 저장된 수의 개수와 같거나 1 많게 함
                if(maxHeap.size == minHeap.size)
                    maxHeap.offer(num)
                else
                    minHeap.offer(num)

                //  최대 힙의 최댓값이 최소 힙의 최솟값보다 작아야 함
                //  최대 힙의 최댓값이 최소 힙의 최솟값보다 클 경우 둘을 swap
                if(maxHeap.isNotEmpty() && minHeap.isNotEmpty() && maxHeap.peek() > minHeap.peek()) {
                    // println("if")
                    val max = maxHeap.poll()
                    val min = minHeap.poll()

                    maxHeap.offer(min)
                    minHeap.offer(max)
                }

                if(it % 2 == 0) //  전체 데이터 개수가 홀수일 경우
                    sb.append("${maxHeap.peek()} ")
            }

            sb.append("\n")
        }

        print(sb)
    }
}

fun main() {
    Main().solve()
}
```