# 24_03_10_daily_certification

```
[koreii] #70 데일리인증
알고리즘 & 코딩 테스트 대비
- Greedy, Stringm Two-Pointers
```

# Problem Solving (Algorithm & SQL)

**BOJ 6137 문자열 생성**

[6137번: 문자열 생성](https://www.acmicpc.net/problem/6137)

주어진 길이 n인 문자열 S의 0번째 index에 start, n-1번째 index에 end를 놓고, start 또는 end에 있는 문자를 문자열 T에 하나씩 추가한다. 

**예제 1**

![ex1.jpeg](24_03_10_daily_certification%20950705be065441aaaa2d2b727728426a/ex1.jpeg)

둘 중 사전 순으로 더 앞서는 문자를 추가하면 된다. start의 문자를 추가했으면 start를 1 증가시키고, end에 있는 문자를 추가했으면 end를 1 감소시키면 된다. start가 end보다 커질 때까지 반복한다.

![ex1_1.jpeg](24_03_10_daily_certification%20950705be065441aaaa2d2b727728426a/ex1_1.jpeg)

문제는 start와 end가 가리키는 두 문자가 같을 때 발생한다. T에 추가하는 문자야 어차피 똑같지만 start를 1 증가시키는 것과 end를 1 감소시키는 것에 따라 이후 결과가 달라질 수 있다. 다음 그림이 그 예시다. 

![ex1_2.jpeg](24_03_10_daily_certification%20950705be065441aaaa2d2b727728426a/ex1_2.jpeg)

start와 end가 가리키는 문자가 둘 다 C로 같을 때 start를 움직이면 T의 결과가 ABCCBD가 되고, end를 움직이면 T의 결과가 ABCBCD가 된다. end를 움직여야 함을 알 수 있다.

start와 end가 가리키는 문자가 같을 경우, start + 1과 end가 가리키는 문자를 비교한 결과와, start와 end - 1가 가리키는 문자를 비교한 결과를 통해 start를 움직일지, end를 움직일지 정하는 코드를 작성해봤다.

**실패한 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_6137 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()

    private var n = 0
    private lateinit var str : CharArray
    private lateinit var res : CharArray

    private var start = 0
    private var end   = 0
    private var idx   = 0

    fun solve() {
        n = br.readLine().toInt()

        end = n - 1
        str = CharArray(n)
        res = CharArray(n)

        repeat(n) {
            str[it] = br.readLine()[0]
        }

        while(start <= end) {
            if(str[start] < str[end])
                res[idx] = str[start++]
            else if(str[start] > str[end])
                res[idx] = str[end--]
            else {
                res[idx] = str[start]

                if(start == end)    //   어차피 마지막 문자이니까 아무거나 해도 상관없음
                    start++         //   start 1 증가
                else {    //    start < end
                    // 1. end를 1 감소시켰을 경우
                    val endLeft = start
                    val endRight = end - 1
                    val endNext = str[endLeft].coerceAtMost(str[endRight])

                    // 2. start를 1 증가시켰을 경우
                    val startLeft = start + 1
                    val startRight = end
                    val startNext = str[startLeft].coerceAtMost(str[startRight])

                    if(endNext < startNext)
                        end--
                    else
                        start++
                }
            }

            idx++
        }

        sb.append(String(res))
        println(sb)
    }
}

fun main() {
    BOJ_6137().solve()
}
```

게시판 반례로 DDCADD가 있다.

위의 코드대로라면 다음과 같이 동작한다. 끝까지 볼 필요도 없이 중간에 문제가 발생한다

![counter.jpeg](24_03_10_daily_certification%20950705be065441aaaa2d2b727728426a/counter.jpeg)

정상적으로 만들어졌다면 A가 왔어야 할 자리에 C가 오게 된다.

한 번은 start를 이동시켰을 때와 end를 이동시켰을 때 다음 결과를 비교하면 되지만 그 결과마저 같다면 정답을 도출할 수 없다.

따라서 start가 가리키는 문자와 end가 가리키는 문자가 같을 경우, 둘이 달라질 때까지 둘 다 이동시켜본다. 둘이 달라질 경우 start가 가리키는 문자가 더 작을 경우 start를 실제로 이동시키고, 그렇지 않을 경우 end를 실제로 이동시킨다.

실제로 start와 end를 이동시키기 보단 새로운 변수 s, e를 선언해보자.

![counter_solve.jpeg](24_03_10_daily_certification%20950705be065441aaaa2d2b727728426a/counter_solve.jpeg)

**성공한 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_6137 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()

    private var n = 0
    private lateinit var str : CharArray
    private lateinit var res : CharArray

    private var start = 0
    private var end   = 0
    private var idx   = 0

    fun solve() {
        n = br.readLine().toInt()

        end = n - 1
        str = CharArray(n)
        res = CharArray(n)

        repeat(n) {
            str[it] = br.readLine()[0]
        }

        while(start <= end) {
            if(str[start] < str[end])   //  start가 더 앞설 경우
                res[idx] = str[start++] //  start 추가 후 start 1 증가
            else if(str[start] > str[end])  //  end가 더 앞설 경우
                res[idx] = str[end--]       //  end 추가 후 end 1 감소
            else {  //  start와 end가 같을 경우
                var s = start + 1
                var e = end - 1
                var find = false

                while(s < e) {
                    if(str[s] != str[e]) {
                        if(str[s] > str[e]) //  end를 감소시켰을 때 더 작은 문자가 먼저 나오면
                            res[idx] = str[end--]   //  end 추가 후 end 1감소
                        else                //  start를 증가시켰을 때 더 작은 문자가 먼저 나올 경우
                            res[idx] = str[start++] //  start 추가 후 start 1 증가

                        find = true
                        break
                    }

                    s++
                    e--
                }

                if(!find)
                    res[idx] = str[start++]
            }

            idx++
        }

        idx = 0
        var cnt = 0
        while(cnt < 80 && idx < n) {
            sb.append(res[idx++])
            cnt++

            if(cnt == 80) {
                cnt = 0
                sb.append("\n")
            }
        }

        println(sb)
    }
}

fun main() {
    BOJ_6137().solve()
}
```