# 24_02_23_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 10473 읽씹 멈춰!**

[27377번: 읽씹 멈춰!](https://www.acmicpc.net/problem/27377)

0부터 시작해서 증가시켜나가며 n을 만들어나갈 생각보다는 n부터 시작해서 n을 줄여나가며 0으로 도달하는 최소 시간을 찾는 것이 쉽다.

0부터 시작하면 풀수 없는지는 잘 모르겠지만, 확실히 n으로부터 역산하는게 홀수일 때 반드시 -1 처리하게 되므로 쉽다.

풀이를 참고하면서 의아했던 점은 현재 수가 짝수일 때 n / 2로 1씩 빼서 도달하는 시간과 절반으로 줄이는 시간만 비교하는 것이었다. 일부는 빼고 남은 값을 절반으로 줄이는 식으로 접근하면 안되는 걸까? 

이게 Greedy일 것 같다.

![ans.jpeg](24_02_23_daily_certification%20de50115213eb42b687ec91b166955334/ans.jpeg)

아무튼 위 그림처럼 

**현재 수가 홀수이면 1을 빼고(0으로 도달하기 위해선 반드시 한 번은 빼야 한다.)**

**현재 수가 짝수이면 1씩 n / 2 번 빼는 시간과 절반으로 줄이는 시간을 비교해서 적은 시간이 드는 방법으로 한다.**

n이 최대 10^18이고 s, t가 최대 10^9이므로 (n / 2) * s만 해도 long 범위를 넘어갈 수 있다. 정답 시간이 long 범위를 넘어갈 수 있음은 당연하고 중간 연산의 비교 과정도 BigInteger를 사용해 비교해야 한다.

**코드**

```kotlin
import java.util.*
import java.io.*
import java.math.BigInteger

class BOJ_27377 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var t = 0
    private var n = 0L               //  보낼 말의 개수
    private var sTime = 0L           //  1개 추가하는 시간
    private var tTime = 0L           //  2배로 복사하는 시간
    private var ans = BigInteger.ZERO

    fun solve() {
        t = br.readLine().toInt()

        repeat(t) {
            n = br.readLine().toLong()
            ans = BigInteger.ZERO

            tokens = StringTokenizer(br.readLine())
            sTime = tokens.nextToken().toLong()
            tTime = tokens.nextToken().toLong()

            //  n에서부터 시작해서 0으로 역산함
            //  그 최소시간 찾기
            while(n > 0L) {
                if (n % 2 == 1L) {   //  현재 n이 홀수일 경우 최소 한 번 그냥 쓰기를 했다는 뜻임
                    ans = ans.add(BigInteger(sTime.toString()))
                    n-- //  1번 적는 것 취소
                } else {  //  현재 n이 짝수일 경우 n / 2번 그냥 쓰기를 해서 왔을 수도 있고, 2배로 복사를 했을 수도 있음
                    ans = if (BigInteger((n / 2).toString()).multiply(BigInteger(sTime.toString()))
                            .compareTo(BigInteger(tTime.toString())) == -1
                    ) //  n / 2 번 직접 쓰는 게 더 빠를 경우
                        ans.add(BigInteger(sTime.toString()).multiply(BigInteger((n / 2).toString())))
                    else    //  2배로 복사하는 게 더 빠를 경우
                        ans.add(BigInteger(tTime.toString()))

                    n /= 2
                }
            }

            sb.append("${ans}\n")
        }

        print(sb)
    }
}

fun main() {
    BOJ_27377().solve()
}
```