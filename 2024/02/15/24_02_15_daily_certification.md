# 24_02_15_daily_certification

# JPQL 기본 문법

# Typescript

# Problem Solving (Algorithm & SQL)

**BOJ 20437 문자열 게임 2**

[20437번: 문자열 게임 2](https://www.acmicpc.net/problem/20437)

‘a’~’z’ 중 아무 문자 ch가 정확히 k개 포함하는 연속 부분 문자열의 최대 길이와 최소 길이를 구하는 것이 문제이다. 이때 최대 길이의 경우 가장 앞의 문자도 ch, 가장 뒤의 문자도 ch여야 한다.

조건에 명시되지는 않았지만 ch가 정확히 k개 포함된 가장 짧은 연속 문자열도 가장 앞의 문자, 뒤의 문자가 모두 ch이다. 생각해보면 당연하다.

주어진 문자열에 포함된 소문자가 총 몇개 쓰였는지 확인한다. 그리고 k개보다 적게 사용된 문자는 후보가 될 수 없으니 배제한다.

![Untitled](24_02_15_daily_certification%205c915c8806cd4291b5208109739252db/Untitled.png)

예제 1은 k가 2이고 2개 이상 존재하는 문자는 a,  o, r,  u다. 각 문자가 나타났던 index를 저장해서 가장 처음 존재하는 문자의 index와 k번째에 존재하는 문자의 index의 차에 1을 더해주고 이 값을 max, min과 비교하며 갱신한다.

a의 경우 index 5, 8, 13에 저장되어 있고 k=2 이므로 가능한 문자열 길이는 8-5+1=4, 13-8+1=6이다.

![Untitled](24_02_15_daily_certification%205c915c8806cd4291b5208109739252db/Untitled%201.png)

k가 2보다 클 경우도 고려해 ch의 시작 index를 잘 정해야 한다. 예제 2에서 a의 경우 총 5개이고 k=3이다.

a의 index는 0, 2, 3, 4, 6이므로 가능한 문자열 길이는 3-0+1=4, 4-2+1=3, 6-3+1=4이다.

k=1인 경우 어떤 문자를 포함하건 그 문자가 시작이자 끝이다. 따라서 길이는 최대, 최소 모두 1이다.

주어진 문자열에 k개 이상인 문자가 하나도 없을 경우 -1을 출력한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_20437 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var t = 0           //  테스트 케이스 개수
    private var input = ""      //  입력 문장
    private val alphList = Array(26) { ArrayList<Int>() }   //  alphList[ch.code - 'a'.code] : input에서 ch의 index 모아둔 리스트
    private var k = 0

    private var max = -1
    private var min = 10_001

    fun solve() {
        t = br.readLine().toInt()

        while(t-- > 0) {
            input = br.readLine()
            k = br.readLine().toInt()

            if(k == 1) {   //   포함된 알파벳이 정확히 1개인 경우
                sb.append("1 1\n")
                continue
            }

            max = -1
            min = 10_001
            repeat(26) {
                alphList[it].clear()
            }

            for(idx in input.indices)
                alphList[input[idx].code - 'a'.code].add(idx)

            for(i in 0 ..< 26) {
                if(alphList[i].size < k)    //  i번째 알파벳이 k개 미만일 경우 skip
                    continue

                for(start in 0 .. alphList[i].size - k) {
                    val end = start + k - 1
                    val diff = alphList[i][end] - alphList[i][start] + 1

                    min = min.coerceAtMost(diff)
                    max = max.coerceAtLeast(diff)
                }
            }

            if(max == -1 || min == 10_001)  //  해당하는 부분 문자열이 없을 경우
                sb.append("-1\n")
            else
                sb.append("$min $max\n")
        }

        print(sb)
    }
}

fun main() {
    BOJ_20437().solve()
}
```

# 독서

**내가 틀릴 수도 있습니다. -비욘나티코린데블라드-**

**Q. 오늘 읽은 부분 중 기억에 남는 문장?**