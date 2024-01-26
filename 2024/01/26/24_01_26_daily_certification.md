# 24_01_26_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1700 멀티탭 스케줄링**

[1700번: 멀티탭 스케줄링](https://www.acmicpc.net/problem/1700)

페이지 교체 알고리즘을 활용하여 현재 멀티탭에 꽂혀 있는 전기 용품 중 가장 나중에 사용될 전기 용품을 뽑으면 플러그 뽑는 횟수를 최소화할 수 있었다.

멀티탭에 꽂혀 있는 전기 용품은 set으로 관리해서, set의 크기가 n 이상이면 기존에 꽂혀 있는 전기 용품 중 가장 나중에 사용될 전기 용품을 빼주는 방식으로 구현했다.

각 전기 용품이 사용될 순서는 전기 용품마다 큐를 만들어서 큐 안에 순서대로 저장해두었다.

주의할 점은 set의 크기가 n보다 작아서 빼지 않고 꽂을 수 있는 경우, 이미 꽂혀져 있는 경우, 빼야 하는 제품들 중 추후 더 이상 사용되지 않는 경우 등이 있는데 모든 경우에서 결국 현재 전기 용품의 순서를 큐에서 제거해줘야 한다. 각 분기별로 처리하다 보면 누락되는 분기가 있어 오답을 낼 수 있다.

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 101 //  최대 멀티탭 구멍 개수, 전기 용품 개수 100

class BOJ_1700 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  멀티탭 구멍 개수
    private var k = 0   //  전기 용품 총 사용 횟수

    private lateinit var orders : IntArray //   orders[i] : i번째에 사용하는 전기 용품
    private val itemSet = HashSet<Int>()    //  플러그 꽂힌 전기 용품 목록, 최대 n개 꽂혀있을 수 있음
    private val itemOrder = Array(MAX) { LinkedList<Int>() }    //  itemOrder[i] : 전기 용품 i의 사용 순서

    private var ans = 0 //  플러그 뽑아야 하는 최소 횟수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        orders = IntArray(k)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< k) {
            val item = tokens.nextToken().toInt()
            orders[i] = item
            itemOrder[item].add(i)
        }

        for(i in 0 ..< k) {
            val item = orders[i]    //  i번째 순서에 사용할 전기 용품

            if(!itemSet.contains(item)) {   //  해당 전기 용품이 이미 꽂혀있지 않을 경우
                if(itemSet.size == n) {  //  빈 구멍이 없을 경우 하나 뽑아야 함
                    var latestOrder = 0 //  가장 늦게 사용될 전기 용품 순서
                    var latestItem = 0  //  가장 늦게 사용될 전기 용품

                    for(outItem in itemSet) {  //  이미 꽂혀 있는 전기 용품 중 대신 뽑을 전기용품 찾기
                        val order = if(itemOrder[outItem].isEmpty()) Int.MAX_VALUE else itemOrder[outItem].peek()

                        if(order > latestOrder) {   //  해당 전기 용품이 가장 늦게 사용될 경우
                            latestOrder = order
                            latestItem = outItem
                        }
                    }

                    itemSet.remove(latestItem)  //  가장 나중에 사용되는 전기 용품 제거
                    ans++   //  빼는 횟수 1 증가
                }

                itemSet.add(item)   //  뽑고 생긴 빈 구멍에 꽂기
            }

            itemOrder[item].pollFirst() //  item의 이번 순서는 끝남
        }

        println(ans)
    }
}

fun main() {
    BOJ_1700().solve()
}
```
