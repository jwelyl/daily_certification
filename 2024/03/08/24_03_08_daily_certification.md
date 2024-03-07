# 24_03_08_daily_certification

```
[koreii] #68 데일리인증
알고리즘 & 코딩 테스트 대비
- Disjoint Set & Greedy
- 모듈로 연산, 오버플로우 방지
```

# Problem Solving (Algorithm & SQL)

**BOJ 12963 달리기**

[12963번: 달리기](https://www.acmicpc.net/problem/12963)

솔직히 말하자면 내 힘으로는 풀지 못했다. 핵심적인 로직은 다음 블로그를 참고했다.

[BOJ)12963 달리기](https://jason9319.tistory.com/285)

핵심 로직은 간선을 크기가 큰 간선부터 추가해보면서 0과 N-1이 연결되면 그 비용을 정답에 더한 후 간선을 제거하고, 연결되지 않는다면 간선을 추가한 상태로 두는 것이다. 위 블로그에서는 간선 추가 시 DFS로 0과 N-1이 연결되는지 확인한 후 연결된다면 간선을 제거하는 방식으로 구현했다. 간선을 제거하는 것은 익숙하지 않아서 다른 풀이를 생각해보다가 분리집합을 이용하게 되었다.

그럼에도 올리는 이유는 다음과 같다.

 **임의의 두 정점 v1, v2를 연결했을 때, 어떤 두 정점 start와 end가 연결될 수 있는지 판단하는 문제는 여태까지 풀어본 분리집합 문제에서는 없었다. 크게 어렵진 않았지만 참신했다.**

등비급수의 합을 이용했다. 이전 항까지의 합을 다 더해도 다음 항 하나만 못한 성질을 이용해 문제를 Greedy하게 해결할 수 있었다.

**등비급수 합 성질 이용했던 문제**

[23258번: 밤편지](https://www.acmicpc.net/problem/23258)

모듈러 연산을 하면서 자료형을 잘 설정해야 했다. 나누는 수 1,000,000,007은 Int 범위이므로 나눈 나머지도 Int임이 보장되지만 매번 3배씩 곱해지기 때문에 이전 값이 나머지 값이여도 3을 곱하는 중간 계산 과정에서 오버플로우가 발생할 수 있었다. 따라서 Long으로 설정해야 했다.

**중간계산 과정에서 오버플로우 발생했던 문제**

[24542번: 튜터-튜티 관계의 수](https://www.acmicpc.net/problem/24542)

가장 간선이 많은 예제 5로 설명을 해보겠다.

![ex5.jpeg](24_03_08_daily_certification%203a21c1c1ef2e43199e40954a7fce0eaf/ex5.jpeg)

가장 크기가 큰 간선부터 추가해본다. 간선을 추가했을 때 정점 0과 N-1이 연결되었다면 더 작은 크기의 간선 없이도 0에서부터 N-1까지 도달할 수 있다는 뜻이다. 만약 도달할 수 없다면 해당 간선을 경로에 추가한다. 해당 간선을 경로에 추가해봤자 0과 N-1은 이어지지 않고, 후에 크기가 더 작은 간선이 경로에 추가되게 되므로 최대 크기는 반드시 현재 간선 크기보다 작게 된다. (3의 거듭제곱 꼴이므로)

위 예제에서는 간선 크기가 가장 큰 8번부터 4번까지를 다 추가해서 연결해도 0과 N-1이 연결되지 않았다.

![ex5_1.jpeg](24_03_08_daily_certification%203a21c1c1ef2e43199e40954a7fce0eaf/ex5_1.jpeg)

간선 8~4까지 다 연결한 상태에서 간선 3을 추가하면 비로소 0과 N-1이 연결된다. 최소 크기 간선이 0부터 N-1까지 도착 가능한 사람 수를 결정하므로 27을 추가한다. 그리고 간선 3은 실제로 추가하지는 않는다. 그래야 0과 N-1이 연결되지 않은 상태에서 다음 간선을 추가했을 때 연결되는지 판단할 수 있다.

![ex5_2.jpeg](24_03_08_daily_certification%203a21c1c1ef2e43199e40954a7fce0eaf/ex5_2.jpeg)

간선 3은 빠진 상태에서 간선 2를 연결해도 0과 N-1이 연결된다. 따라서 9를 추가한다.

![ex5_3.jpeg](24_03_08_daily_certification%203a21c1c1ef2e43199e40954a7fce0eaf/ex5_3.jpeg)

간선 2는 빠진 상태에서 간선 1를 연결해도 0과 N-1이 연결된다. 따라서 3을 추가한다.

![ex5_4.jpeg](24_03_08_daily_certification%203a21c1c1ef2e43199e40954a7fce0eaf/ex5_4.jpeg)

간선 1이 빠진 상태에서 간선 0을 추가해봤자 0과 N-1은 연결되지 않는다. 따라서 추가하지 않는다.

두 정점 v1, v2를 잇는 간선을 추가했을 때, 0과 N-1이 연결되는지 파악하는 코드는 다음과 같다.

```kotlin
val v1DS = findDS(v1)
val v2DS = findDS(v2)   //  교차로의 두 정점이 속한 각각의 분리집합의 우두머리

//  현재 0과 n - 1이 연결되지 않았음은 보장이 되는 상태
val start = findDS(0)   //  시작 정점이 포함된 분리집합의 우두머리
val end = findDS(n - 1) //  끝 정점이 포함된 분리집합의 우두머리

if((start == v1DS && end == v2DS) || (start == v2DS && end == v1DS))    //  v1과 v2가 연결되었을 때 start와 end가 연결된다면
    //  0에서 n-1로 현재까지 간선 n-1 부터 i까지만 이용해서 갈 수 있으므로 간선 i 크기 추가하고 실제 연결은 하지 않음(더 작은 크기 간선 추가해도 되나 확인)
    ans = (ans % MOD + (edgeCost[i] % MOD).toInt()) % MOD
else
    //  간선 i 추가하는 것만으로는 갈 수 없음. 간선 i까지는 연결하고 더 작은 크기 간선 추가해봐야 함
    unionDS(v1DS, v2DS)
```

v1이 속한 분리집합의 우두머리를 v1DS, v2가 속한 분리집합의 우두머리를 v2DS라고 하고 마찬가지로 0이 속한 분리집합의 우두머리를 start, n-1이 속한 분리집합의 우두머리를 end라고 하자.

**start == v1DS && end == v2DS이면 0은 v1과 같은 분리집합이고, n-1은 v2와 같은 분리집합이다. 따라서 두 분리집합을 합치면 0과 n-1도 연결된다.**

**start == v2DS && end == v1DS인 경우도 마찬가지이다.**

**전체 코드**

```kotlin
import java.util.*
import java.io.*

const val MOD = 1_000_000_007

class BOJ_12963 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  교차로 개수
    private var m = 0   //  도로 개수

    private lateinit var ds : IntArray          //  ds[i] : i가 속한 분리 집합의 우두머리
    private lateinit var edgeCost : LongArray   //  edgeCost[i] : (i번째 간선 통과 가능 사람 수) % MOD
    private lateinit var v1Arr : IntArray       //  v1Arr[i] : i번째 간선의 왼쪽 정점
    private lateinit var v2Arr : IntArray       //  v2Arr[i] : i번째 간선의 오른쪽 정점

    private var ans = 0 //  정답

    fun solve() {
        tokens = StringTokenizer(br.readLine())

        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        ds = IntArray(n) { it }
        edgeCost = LongArray(m)
        v1Arr = IntArray(m)
        v2Arr = IntArray(m)

        if(m > 0) { //  간선이 1개 이상일 경우
            edgeCost[0] = 1
            for (i in 1..<m)
                edgeCost[i] = (edgeCost[i - 1] * 3) % MOD   //  간선 비용 정하기, 모듈러 MOD 값은 최대 1,000,000,006이므로 3을 곱하면 Int형일 경우 overflow 발생 가능
        }

        for(i in 0 ..< m) { //  간선 입력받기
            tokens = StringTokenizer(br.readLine())
            v1Arr[i] = tokens.nextToken().toInt()
            v2Arr[i] = tokens.nextToken().toInt()
        }

        for(i in m - 1 downTo 0) {  //  가장 사람 많이 지나갈 수 있는 도로부터
            val v1 = v1Arr[i]
            val v2 = v2Arr[i]   //  교차로의 두 정점

            val v1DS = findDS(v1)
            val v2DS = findDS(v2)   //  교차로의 두 정점이 속한 각각의 분리집합의 우두머리

            //  현재 0과 n - 1이 연결되지 않았음은 보장이 되는 상태
            val start = findDS(0)   //  시작 정점이 포함된 분리집합의 우두머리
            val end = findDS(n - 1) //  끝 정점이 포함된 분리집합의 우두머리

            if((start == v1DS && end == v2DS) || (start == v2DS && end == v1DS))    //  v1과 v2가 연결되었을 때 start와 end가 연결된다면
                //  0에서 n-1로 현재까지 간선 n-1 부터 i까지만 이용해서 갈 수 있으므로 간선 i 크기 추가하고 실제 연결은 하지 않음(더 작은 크기 간선 추가해도 되나 확인)
                ans = (ans % MOD + (edgeCost[i] % MOD).toInt()) % MOD
            else
                //  간선 i 추가하는 것만으로는 갈 수 없음. 간선 i까지는 연결하고 더 작은 크기 간선 추가해봐야 함
                unionDS(v1DS, v2DS)
        }

        println(ans)
    }

    //  v가 속한 분리집합의 우두머리 찾기
    private fun findDS(v : Int) : Int {
        if(v == ds[v])  //  자기 자신이 우두머리일 경우
            return v

        ds[v] = findDS(ds[v])   //  자신의 우두머리의 우두머리를 찾아서 자신의 우두머리로 삼음
        return ds[v]
    }

    //  미리 찾은 분리집합의 우두머리를 받아서 같은 우두머리가 아닐 경우 합치기
    private fun unionDS(v1 : Int, v2 : Int) {
        if(v1 != v2)
            ds[v2] = v1
    }
}

fun main() {
    BOJ_12963().solve()
}
```
Platinum 3 문제를 스스로의 힘만으로 푼 것은 아니지만, 그만한 난이도 문제를 다뤄보면서 배운 건 많은 문제였다.