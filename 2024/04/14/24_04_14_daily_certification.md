# 24_04_14_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 25634 전구 상태 뒤집기**

[25634번: 전구 상태 뒤집기](https://www.acmicpc.net/problem/25634)

일단 켜져 있는 전구들의 밝기 합을 sum에 저장한다.

dp 테이블을 정의한다. dp[i]는 1번 전구부터 i번 전구까지 중 문제의 조건에 맞게 전구를 뒤집었을 때 증가하는 최대 밝기이다.

**dp[i]는 다음과 같이 정의할 수 있다.**

1. i번째 전구가 켜져 있을 경우
    1. i번째 전구를 끄면 밝기 변화는 -a[i]가 된다.
    2. dp[i]와 dp[i-1] - a[i] 중 큰 값을 dp[i]로 한다.
2. i번째 전구가 꺼져 있을 경우
    1. i번째 전구를 키면 밝기 변화는 a[i]가 된다.
    2. dp[i]와 dp[i-1] + a[i] 중 큰 값을 dp[i]로 한다.

dp 중 최댓값이 밝기 변화 중 최댓값이다. 이를 초기의 밝기에 더해주면 뒤집었을 때 최대 밝기가 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_25634 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0;  //  전구 개수
    private lateinit var a : IntArray           //  전구 밝기
    private lateinit var b : BooleanArray       //  true면 켜진 전구, false면 꺼진 전구

    private var sum = 0L    //  초기에 켜져 있는 전구 누적합
    private lateinit var dp : LongArray         //  dp[i] : 0 ~ i까지 중에서 일부 구간을 뒤집었을 때 밝기 변화 최대

    fun solve() {
        n = br.readLine().toInt()

        a = IntArray(n + 1)
        b = BooleanArray(n + 1)
        dp = LongArray(n + 1)

        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. n)
            a[i] = tokens.nextToken().toInt()
        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. n)
            b[i] = tokens.nextToken().toInt() == 1

        //  초기 주어진 상태 켜진 전구 합
        for(i in 1 .. n) {
            if(b[i])
                sum += a[i]
        }

        for(i in 1 .. n) {
            if(b[i]) {  //  i번째 전구가 켜졌다면
                dp[i] = -a[i].toLong()    //  dp[i]를 일단 i번째 전구만 뒤집는 걸로 설정
                dp[i] = dp[i].coerceAtLeast(dp[i - 1] - a[i])   //  (1, i-1)까지 처리했을 때 최댓값 - i번째 전구 밝기 와 비교
            }
            else {  //  i번째 전구가 꺼졌다면
                dp[i] = a[i].toLong()    //  dp[i]를 일단 i번째 전구만 뒤집는 걸로 설정
                dp[i] = dp[i].coerceAtLeast(dp[i - 1] + a[i])   //  (1, i-1)까지 처리했을 때 최댓값 + i번째 전구 밝기 와 비교
            }
        }

        var diff = dp[1]    //  밝기 합이 가장 크게 증가하는 변화

        for(i in 2 .. n)
            diff = diff.coerceAtLeast(dp[i])    //  밝기 합 최대 증가 찾기

        println(sum + diff) //  현재 밝기 + 최대 증가량
    }
}

fun main() {
    BOJ_25634().solve()
}
```