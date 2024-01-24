# 24_01_24_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1202 보석 도둑**

[1202번: 보석 도둑](https://www.acmicpc.net/problem/1202)

보석들을 보석 무게 순으로 오름차순 정렬, 가방들을 가방에 담을 수 있는 무게 순으로 오름차순으로 정렬했을 때, 담을 수 있는 무게가 가장 작은 가방부터 담을 수 있는 모든 보석을 담아본다. 현재 가방에 담을 수 있는 보석들을 담는 역할을 PriorityQueue가 한다. PriorityQueue의 우선 순위는 보석의 가치로 하여, 가장 가치 있는 보석부터 꺼내도록 한다.

매 가방마다 담을 수 있는 보석들을 pq에 저장하고, 그 중에서 가장 가치 있는 보석을 pq에서 꺼내어 가방에 담는다. pq에 남은 보석들은 자동으로 담을 수 있는 무게가 더 큰 다음 가방에 담을 수 있는 보석들이 된다. 이 과정을 다음 모든 가방에 대해 반복한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1202 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  보석 개수
    private var k = 0   //  가방 개수

    private val gemList = ArrayList<Gem>()  //  보석 리스트 (무게가 작은 순으로 정렬)
    private val bagList = ArrayList<Int>()  //  가방 리스트 (가방에 담을 수 있는 무게가 작은 순으로 정렬)

    //  현재 가방 무게일 때, 현재 가방 무게에 담을 수 있는 보석 중 가장 가치가 큰 보석을 꺼내줌
    private val pq = PriorityQueue<Gem> { it1, it2 -> it2.value - it1.value }
    private var ans = 0L  // 훔칠 수 있는 보석들의 가치 합의 최대

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            gemList.add(Gem(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        repeat(k) {
            bagList.add(br.readLine().toInt())
        }

        gemList.sortBy { it.mass }
        bagList.sort()

        var gemIdx = 0

        for(i in 0 ..< k) { //  i번째로 가벼운 가방에 대하여
            val weight = bagList[i] //  i번째 가방에 담을 수 있는 무게

            //  i번째 가방에 담을 수 있는 모든 보석 담기
            while(gemIdx < n && gemList[gemIdx].mass <= weight) //  현재 보석을 i번째 가방에 담을 수 있을 경우
                pq.offer(gemList[gemIdx++])

            //  i번째 가방에 담을 수 있는 모든 보석 담은 후
            if(pq.isNotEmpty()) //  담을 수 있는 보석이 있을 경우
                ans += pq.poll().value  //  i번째 가방에 담을 수 있는 모든 보석들 중 가장 가치 있는 걸 담음, 나머지 보석들은 i+1번째 가방에 담을 수 있는 보석들이 됨
        }

        println(ans)
    }

    private class Gem(val mass : Int, val value : Int)
}

fun main() {
    BOJ_1202().solve()
}
```