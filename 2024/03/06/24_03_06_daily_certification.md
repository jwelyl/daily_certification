# 24_03_06_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 16120 PPAP**

[16120번: PPAP](https://www.acmicpc.net/problem/16120)

예제 1을 보고 생각해낸 해결 방법은 다음과 같다.

input을 차례로 읽어 한 문자씩 스택에 push하고 스택 최상위에 쌓인 문자 4개가 PPAP이면 그 4개를 스택에서 pop하고 P를 다시 스택에 push한다.

![ex1.jpeg](24_03_06_daily_certification%206ef21cd7ed464369814accc69f922cf7/ex1.jpeg)

최종적으로 스택에 P만 남으면 input이 PPAP 문자열이다.

스택 최상위 4개 문자가 PPAP인지를 어떻게 알 수 있을지가 문제다.

스택 길이가 4 이상일 때 마지막 4개 문자랑 PPAP를 비교하는 방식으로 구현해봤다. 굳이 스택을 사용할 필요없이 StringBuilder를 사용하면 될 것 같아서 구현해봤다.

**실패한 코드 (시간 초과)**

```kotlin
import java.util.*
import java.io.*

class BOJ_16120 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()

    private var input = ""

    fun solve() {
        input = br.readLine()

        for(ch in input) {
            sb.append(ch)

            if(sb.length >= 4) {
                if(sb.indexOf("PPAP") == sb.length - 4) {
                    sb.deleteRange(sb.indexOf("PPAP"), sb.length)
                    sb.append('P')
                }
            }
        }

        println(if(sb.toString() == "P") "PPAP" else "NP")
    }
}

fun main() {
    BOJ_16120().solve()
}
```

시간 초과로 실패했다.

![ex1_revised.jpeg](24_03_06_daily_certification%206ef21cd7ed464369814accc69f922cf7/ex1_revised.jpeg)

P일 경우 스택에 넣어주고, A일 경우 스택에서 P 2개를 제거하도록 구현하였다. A일 때 스택에 P가 2개 이상 없거나, A 다음에 또 A가 나올 경우에는 NP를 출력하도록 하였다. 최종적으로 스택에 P 1개만 남으면 PPAP이다.

**실패한 코드 (틀렸습니다.)**

```kotlin
import java.util.*
import java.io.*

class BOJ_16120 {
    private val br = BufferedReader(InputStreamReader(System.`in`))

    private lateinit var input : CharArray
    private var afterA = false
    private var ok = true

    private val stack = Stack<Char>()

    fun solve() {
        input = br.readLine().toCharArray()

        for(ch in input) {
            if(ch == 'A') {    //    A일 경우
                if(afterA || stack.size < 2) {    //    A가 나오면 안 될 떄 A가 나온 경우
                    ok = false
                    break
                }

                stack.pop()
                stack.pop()    //    스택에 존재하는 P 2개 제거
                afterA = true
            }
            else {    //    P일 경우
                afterA = false
                stack.push(ch)
            }
        }

        println(if(ok && stack.size == 1) "PPAP" else "NP")
    }
}

fun main() {
    BOJ_16120().solve()
}
```

위의 코드는 다음과 같은 반례를 생각할 수 있다.

![counter.jpeg](24_03_06_daily_certification%206ef21cd7ed464369814accc69f922cf7/counter.jpeg)

위와 같은 반례를 해결하기 위해서는 A가 나온 다음 문자가 P임을 확인하는 로직이 필요하다.

추가한 코드는 다음과 같다.

**성공한 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_16120 {
    private val br = BufferedReader(InputStreamReader(System.`in`))

    private lateinit var input : CharArray
    private var ok = true

    private val stack = Stack<Char>()

    fun solve() {
        input = br.readLine().toCharArray()

        for(i in input.indices) {
            val ch = input[i]

            if(ch == 'P')            //    P일 경우
                stack.push(ch)       //    스택에 넣기
            else {    //    'A'일 경우, P 2개를 pop하고 다음 문자가 P여야 함
                //    제거할 P가 2개가 없거나, 다음 문자가 없거나, 다음 문자가 P가 아닐 경우
                if(stack.size < 2 || i + 1 >= input.size || input[i + 1] != 'P') {
                    ok = false
                    break
                }

                stack.pop()
                stack.pop()    //    앞의 2개의 P를 제거
            }
        }

        println(if(!ok) "NP" else if(stack.size == 1) "PPAP" else "NP")
    }
}

fun main() {
    BOJ_16120().solve()
}
```

**CLASS 4 문제 중 아래 문제와 비슷한 것 같다. 쿨타임 꽤 돌았으니 다시 풀어봐야 겠다.**

[9935번: 문자열 폭발](https://www.acmicpc.net/problem/9935)
