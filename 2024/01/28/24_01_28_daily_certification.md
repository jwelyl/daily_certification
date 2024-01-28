# 24_01_28_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1700** 멀티탭 스케쥴링

[1700번: 멀티탭 스케줄링](https://www.acmicpc.net/problem/1700)

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 101

class BOJ_1700 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    
    private var n = 0    //    멀티탭 구멍 개수
    private var k = 0    //    전기 용품 총 사용 횟수
    
    private lateinit var orders : IntArray //    orders[i] : i번째 순서에 사용할 전기 용품
    private val orderQueues = Array(MAX) { LinkedList<Int>() }    //    orderQueues[i] : i번째 전기 용품이 사용될 순서 큐
    private val plugged = HashSet<Int>()    //    플러그 꽂힌 전기 용품 set
    
    private var ans = 0    //    전기 용품 플러그 뽑는 최소 횟수
    
    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()
        
        orders = IntArray(k)
        
        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< k) {
            orders[i] = tokens.nextToken().toInt()    //    i번째 순서에 사용될 전기 용품
            orderQueues[orders[i]].offer(i)    //    해당 전기 용품 순서 큐에 i 추가
        }
        
        for(i in 0 ..< k) {
            val item = orders[i]    //    i번쨰 순서에 사용할 전기 용품
            
            if(!plugged.contains(item)) {    //    item이 플러그에 꽂혀있지 않을 경우
                if(plugged.size == n) {    //    이미 멀티탭이 꽉 차서 item을 꽂을 수없을 경우 하나를 빼야 함
                    var latestUsedItem = 0            //    꽂혀 있는 전기 용품들 중 가장 늦게 사용될 전기 용품
                    var latestUsedItemOrder = 0       //    그 전기 용품이 사용될 순서
                    
                    for(other in plugged) {    //    이미 꽂혀있는 다른 전기 용품들 확인
                        val usedItemOrder = if(orderQueues[other].isNotEmpty()) orderQueues[other].peek() else Int.MAX_VALUE    //    해당 전기 용품이 가장 가까운 시간에 사용될 순서
                        
                        if(latestUsedItemOrder < usedItemOrder) {    //    해당 전기 용품이 가장 늦게 사용되면
                            latestUsedItem = other    //    가장 늦게 사용되는 전기 용품 갱신
                            latestUsedItemOrder = usedItemOrder    
                        }
                    }
                    
                    plugged.remove(latestUsedItem)    //    가장 늦게 사용되는 전기 용품 제거
                    ans++    //    플러그 뽑는 횟수 1 증가
                }
                
                plugged.add(item)    //    item의 플러그를 멀티탭에 꽂음
            }
                
            orderQueues[item].poll()    //    item의 이번 순서는 사용했으므로 제거해줌
        }
        
        println(ans)
    }
}

fun main() {
    BOJ_1700().solve()
}
```