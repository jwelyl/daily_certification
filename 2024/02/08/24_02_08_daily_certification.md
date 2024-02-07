# 24_02_08_daily_certification

# Computer Network

## Network Layer

### Hierarchical Routing

![hierarchical_routing.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/hierarchical_routing.jpeg)

### Autonomous Systems (ASes)

고유 번호(Autonomous System Number, ASN) 존재

### Intra-AS Routing Algorithms

네트워크 내부 라우터 간의 라우팅 알고리즘 (AS가 선택함)

목적이 분명함 (최소 cost)

- Link State Algorithm → OSPF
- Distance Vector Algorithm → RIP

### Inter-AS Routing Algorithms

네트워크 사이의 라우팅 알고리즘 (결정 주체가 모호함)

목적이 불분명함 (최소 cost 불가. 한국에서 베이징으로 최소 cost로 가고 싶다고 북한 거칠 순 없음)

정치, 사회, 경제적 논리가 작용함

- BGP

![customervsprovider.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/customervsprovider.jpeg)

AS라고 다 같은 AS가 아니다. 예를 들어 SKT 같은 큰 AS도 있고, 서강대학교 같은 작은 AS도 있다. (갑을 관계?)

Provider가 Customer에게 Internet Access를 제공하고 Customer는 Provider에게 비용을 제공한다.

![peerpeer.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/peerpeer.jpeg)

하지만 SKT와 KT라면? 실제로 누가 더 잘나가고를 떠나서 비슷한 급이다. Peer 관계에서는 비용을 주고 받는 것이 없다.

검은 점선의 traffic의 경우 가운데 AS는 비용을 받지도 않으면서 Internet Access만 제공하므로 전혀 이득이 없다. 이런 traffic은 허용되지 않는다.

Customer, Provider, Peer 모두 상대적이다. SKT가 서강대 상대로는 Provider이지만 AT&T 상대로는 Customer일 수 있다.

### Border Gateway Protocol (BGP)

Network들의 Gateway Router 간의 Routing Protocol이다.

Link State Algorithm, Distance Vector Algorithm과 같은 최소 비용 목적이 아니라 정책에 따라 좌우된다.

![bgp.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/bgp.jpeg)

# Problem Solving (Algorithm & SQL)

**BOJ 13144 List of Unique Numbers**

[13144번: List of Unique Numbers](https://www.acmicpc.net/problem/13144)

풀이는 그림으로 대체한다. 어차피 나 혼자 볼거니까 ㅎㅎ

![2P1.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P1.png)

![2P2.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P2.png)

![2P3.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P3.png)

중복이 발생할 때까지 end 포인터를 이동시키면서 end 포인터로 끝나는 부분 수열들을 더해준다.

중복이 발생할 경우 중복이 사라질 때까지 start 포인터를 이동시킨다.

중복 체크는 visited 배열을 사용했다. 가능한 수가 최대 100,000이므로 가능했는데 만약 더 큰 수의 중복 체크가 필요하다면 HashSet을 사용할 수 있을 것 같다.

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 100_001

class BOJ_13144 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var sequence : IntArray
    private val visited = BooleanArray(MAX)

    private var ans = 0L

    fun solve() {
        n = br.readLine().toInt()
        sequence = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            sequence[i] = tokens.nextToken().toInt()

        var start = 0
        var end = 0

        while(end < n) {
            while(visited[sequence[end]]) {
                visited[sequence[start]] = false
                start++
            }

            while(end < n && !visited[sequence[end]]) {
                ans += (end - start + 1)
                visited[sequence[end]] = true
                end++
            }
        }

        println(ans)
    }
}

fun main() {
    BOJ_13144().solve()
}
```