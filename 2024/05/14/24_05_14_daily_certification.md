# 24_05_14_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE k번째로 작은 쌍의 합**

[https://www.codetree.ai/missions/8/problems/sum-of-kth-smallest-pair/description](https://www.codetree.ai/missions/8/problems/sum-of-kth-smallest-pair/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-kth-smallest-pair/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0
    private var k = 0

    private val list1 = ArrayList<Int>()    //  수열 1
    private val list2 = ArrayList<Int>()    //  수열 2

    private val pq = PriorityQueue<Pair>()   //  최소 합 뽑아낼 pq

    fun solve() {
        tokens = StringTokenizer(br.readLine())

        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        tokens = StringTokenizer(br.readLine())
        repeat(n) {
            list1.add(tokens.nextToken().toInt())
        }
        tokens = StringTokenizer(br.readLine())
        repeat(m) {
            list2.add(tokens.nextToken().toInt())
        }

        list1.sort()
        list2.sort()    //  오름차순 정렬

        //  수열 1의 첫번째 수와 수열 2의 모든 수와의 쌍을 넣음
        for(i in 0 until n)
            pq.offer(Pair(list1[i] + list2[0], i, 0))

        for(i in 1 until k) {
            val minPair = pq.poll() //  (i번째로 합이 작은 쌍 빼기)

            val idx1 = minPair.idx1 //  i번째로 합이 작은 쌍에 쓰인 수열 1의 수 위치
            val idx2 = minPair.idx2 //  i번째로 합이 작은 쌍에 쓰인 수열 2의 수 위치

            if(idx2 + 1 < m)    //  수열 2에서 더 뒤에 있는 수와 조합이 가능할 경우
                pq.offer(Pair(list1[idx1] + list2[idx2 + 1], idx1, idx2 + 1))
        }

        //  k번째로 합이 작은 쌍 빼기
        println(pq.poll().sum)
    }

    private class Pair(val sum : Int, val idx1 : Int, val idx2 : Int) : Comparable<Pair> {
        override fun compareTo(other : Pair) : Int {
            var ret = this.sum.compareTo(other.sum) //  합이 작은 순으로 정렬

            if(ret == 0)    //  합이 같을 경우
                ret = this.idx1.compareTo(other.idx1)   //
            if(ret == 0)
                ret = this.idx2.compareTo(other.idx2)

            return ret
        }
    }
}

fun main() {
    Main().solve()
}
```