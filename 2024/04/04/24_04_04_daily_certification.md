# 24_04_04_daily_certification

```
[koreii] #95 데일리인증
알고리즘 & 코딩 테스트 대비
- Greedy, Bitmasking, Geometric Sequence
```

# Problem Solving (Algorithm & SQL)

**BOJ 1052 물병**

[1052번: 물병](https://www.acmicpc.net/problem/1052)

주어진 n을 이진수로 바꾸었을 때 각 자릿수의 1이 처음에 n개의 물병에 든 물들을 최대한 합친 후의 물병을 나타낸다. 예제 2를 예로 들어보자.\

![ex2_1.jpeg](24_04_04_daily_certification%204982b963a11547c3a97500952cb52a2b/ex2_1.jpeg)

N이 13이므로 물병이 13개 존재하고 각각의 물병에는 물이 1씩 들어있다. 물이 들어있는 물병의 개수를 최소화하며 주어진 규칙대로 물을 합쳐보자. 물병에 든 물의 양은 항상 2의 거듭제곱꼴이 된다는 점을 주목해야 한다.

물이 들어있는 물병의 개수를 최소화하려면 (빈 물병 개수를 최대화하려면), 물이 들어있는 물병에 최대한 많은 물을 넣어야 한다. 물을 많이 넣되 2의 거듭제곱꼴로 많이 넣어야 한다. 결국 이진수로 나타내야 한다.

이진수로 나타내면 다음과 같다.

![ex2_2.jpeg](24_04_04_daily_certification%204982b963a11547c3a97500952cb52a2b/ex2_2.jpeg)

K가 2이므로 물이 든 물병 개수는 최대 2개여야 한다. 물이 가장 많이 든 물병부터 시작해서 K번째 물병은 물이 4만큼 든 물병이다. 그 이후에 물이 든 물병들은(위의 그림에서는 1개지만) 물의 양을 다 합쳐도 K번째 물병에 든 물의 양보다 적다. 그리고 그 양의 합이 K번째 물병에 든 양만큼 되면 K번째 물병과 합쳐져서 물이 든 물병이 K개만 남게 된다.

따라서 K번째 이후 물이 든 물병의 물의 합을 K번째 물병에 든 물의 양 4로 맞춰줘야 하므로 4에서 부족한 만큼 추가하면 된다. 즉 3만큼 구매하면 된다.

정답이 없을 경우 -1을 출력하라고 되어있지만 정답이 없는 경우는 존재하지 않는다.

N 자체가 K보다 적을 경우 굳이 이진수로 변경할 필요 없이 이미 조건을 만족하므로 추가해야 할 물의 양도 0이다. 이진수로 변경한 후에도 1의 개수가 K개보다 적거나 같을 경우 조건을 만족하므로 추가해야 할 물의 양도 0이다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.pow

class BOJ_1052 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var k = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        if(n <= k)
            println(0)
        else {
            var cnt = 0 //  n을 이진수로 바꿨을 때 1의 개수

            val bits = n.toString(2)
            var len = bits.length - 1
            var prev = 0
            var sum = 0

            for(bit in bits) {
                if(bit == '1') {
                    cnt++

                    if(cnt == k)
                        prev = (2.0).pow(len).toInt()   //  cnt개의 비어있지 않은 물병 중 가장 적은 물이 들어있는 물병의 물의 양
                    else if(cnt > k)
                       sum += (2.0).pow(len).toInt()    //  cnt개 이후의 물병에 든 물들 합
                }

                len--
            }

            if(cnt <= k)
                println(0)
            else
                println(prev - sum)
        }
    }
}

fun main() {
    BOJ_1052().solve()
}
```