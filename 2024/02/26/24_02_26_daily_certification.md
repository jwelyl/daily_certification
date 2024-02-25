# 24_02_26_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 20136 멀티탭 스케줄링 2**

[20136번: 멀티탭 스케줄링 2](https://www.acmicpc.net/problem/20136)

일단 결론부터 말하자면 내 스스로 해결하지는 못했다. 1700 멀티탭 스케줄링을 해결한 내용을 바탕으로 시간 복잡도를 줄여보려고 노력했지만 게시판에 존재하는 테스트 케이스 다 못맞춘 것은 물론, 제출 시 시간초과도 경험하였다.

PriorityQueue에서 poll이 아니라 remove로 임의의 element를 제거하는데에는 O(N)의 시간복잡도가 소요된다. 제거할 element를 linear search로 찾는데 O(N)의 시간복잡도가 소요되고, 이를 제거 후 heapify하는데  O(logN)의 시간복잡도가 소요된다.

**실패한 코드 (시간 초과 + 틀렸습니다.)**

```kotlin
import java.util.*
import java.io.*

class BOJ_20136 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  멀티탭 구멍 개수
    private var k = 0   //  전기 용품 총 사용 횟수

    private val powerbar = HashSet<Int>()   //  현재 멀티탭에 꽂혀있는 전자기기
    private lateinit var orders : IntArray  //  전자기기 사용 순서 orders[i] : i번째에 사용할 전자기기
    private lateinit var itemOrders : Array<Deque<Int>> //  itemOrders[i] : i번째 전자기기의 사용 순서

    //  가장 가까운 시간에 사용될 전자기기 번호
    private val pq = PriorityQueue<Int> {
        it1, it2 -> itemOrders[it2].peekFirst().compareTo(itemOrders[it1].peekFirst())
    }

    private var ans = 0 //  멀티탭에서 전자기기 빼는 횟수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        orders = IntArray(k)
        itemOrders = Array(k + 1) { LinkedList() }

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< k) {
            val item = tokens.nextToken().toInt()
            orders[i] = item    //  i번째에 사용되는 전자기기
            itemOrders[item].offerLast(i)   //  전자기기가 i번째 순서에 사용됨
        }
        for(item in 1 .. k) //  더 이상 사용되지 않을 것을 고려
            itemOrders[item].offerLast(Int.MIN_VALUE)

        for(item in 1 .. k)
            pq.offer(item)

        for(item in orders) {   //  사용될 전자기기 확인
//            println("\n멀티탭 = $powerbar")
//            println("현재 사용될 item = $item")
//            println("before pq = $pq")

            if(!powerbar.contains(item)) {  //  현재 사용할 전자기기가 이미 꽂혀있지 않을 경우, 꽂아야 함
                if(powerbar.size == n) {  //  멀티탭에 사용중인 플러그 개수가 n개일 경우, 다른 전자기기를 빼고 사용해야 함
//                    println("nowPq = $pq")
//                    printItemOrders()
                    val lastItem = pq.peek()
//                    println("lastItem = $lastItem")

                    powerbar.remove(lastItem)   //  가장 나중에 쓰일 전자기기 멀티탭에서 제거
                    ans++
//                    println("ans++")
                }

                powerbar.add(item)  //  현재 전자기기 멀티탭에 꽂기
            }

            pq.remove(item)
            itemOrders[item].pollFirst()    //  현재 전자기기의 사용 시간 제거
            pq.offer(item)  //  현재 전자기기의 추후 사용 시간이 갱신되었으므로 pq에 다시 넣기
//            println("after pq = $pq")
        }

        println(ans)
    }

    private fun printItemOrders() {
        println("+++++++++++++++++++++++++++++++++++++++++++")
        for(i in 1 .. k) {
            print("itemOrders[$i] : ")
            println(itemOrders[i])
        }
        println("+++++++++++++++++++++++++++++++++++++++++++")
    }
}

fun main() {
    BOJ_20136().solve()
}
```

remove를 사용해서 시간초과가 발생한 것은 그렇다치더라도 1700 멀티탭 스케줄링의 게시판 예제도 실패한 것은 내가 생각한 로직 자체에 문제가 있다는 것이다. 일단 게시판 예제라도 전부 맞을 수 있게 고쳐본 후에 heapify를 구현한 커스텀 힙을 구현해서 시간복잡도를 줄이도록 해봐야겠다.

다음 블로그를 참고하여 성공 코드를 작성했다.

[[BOJ/20136] 멀티탭 스케줄링 2](https://iot624.tistory.com/161)

### 성공한 코드

```kotlin
import java.util.*
import java.io.*

class BOJ_20136 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  멀티탭 구멍 개수
    private var k = 0   //  전자기기 총 사용 횟수

    private lateinit var orders : IntArray  //  전자기기 사용 순서 orders[i] : i번째에 사용할 전자기기
    private lateinit var itemOrders : Array<PriorityQueue<Int>> //  itemOrders[i] : i번째 전자기기의 사용 순서
    private lateinit var used : BooleanArray    //  used[i] : i가 꽂혀 있으면 true
    private var cnt = 0 //  현재 꽂혀 있는 전자기기 개수

    private val powerbar = PriorityQueue<Item>{ it1, it2 -> it2.order.compareTo(it1.order) }

    private var ans = 0 //  멀티탭에서 전자기기 빼는 횟수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        orders = IntArray(k)
        itemOrders = Array(k + 1) { PriorityQueue() { it1, it2 -> it1.compareTo(it2) } }
        used = BooleanArray(k + 1)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< k) {
            val item = tokens.nextToken().toInt()
            orders[i] = item    //  i번째에 사용되는 전자기기
            itemOrders[item].offer(i)   //  전자기기가 i번째 순서에 사용됨
        }

        for(item in orders) {   //  사용될 전자기기 확인
            if(used[item]) {    //  item이 현재 멀티탭에 꽂혀 있을 경우
                itemOrders[item].poll() //  현재 전자기기 사용 순서 제거
                //  현재 전자기기의 다음 사용 순서 설정해서 넣기(더 이상 사용되지 않으면 Int.MAX_VALUE)
                powerbar.offer(Item(item, if(itemOrders[item].isNotEmpty()) itemOrders[item].peek() else Int.MAX_VALUE))
            }
            else {  //  item이 멀티탭에 꽂혀있지 않을 경우
                if(cnt < n) {   //  멀티탭에 꽂을 수 있을 경우
                    itemOrders[item].poll() //  현재 전자기기 사용 순서 제거
                    //  현재 전자기기의 다음 사용 순서 설정해서 넣기(더 이상 사용되지 않으면 Int.MAX_VALUE)
                    powerbar.offer(Item(item, if(itemOrders[item].isNotEmpty()) itemOrders[item].peek() else Int.MAX_VALUE))
                    cnt++
                    used[item] = true
                } else {    //  멀티탭에 꽂을 수 없을 경우
                    val outItem = powerbar.poll()   //  이미 꽂혀있는 전자기기 중 추후 가장 나중에 사용될 전자기기 뽑기
                    used[outItem.num] = false

                    ans++   //  뽑는 횟수 1 증가

                    itemOrders[item].poll() //  현재 전자기기 사용 순서 제거
                    //  현재 전자기기의 다음 사용 순서 설정해서 넣기(더 이상 사용되지 않으면 Int.MAX_VALUE)
                    powerbar.offer(Item(item, if(itemOrders[item].isNotEmpty()) itemOrders[item].peek() else Int.MAX_VALUE))
                    used[item] = true
                }
            }
        }

        println(ans)
    }

    //  num : 전자기기 번호
    //  order : 전자기기 사용 순서
    private class Item(val num : Int, val order : Int)
}

fun main() {
    BOJ_20136().solve()
}
```