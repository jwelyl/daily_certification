# 24_02_11_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 3745 오름세**

[3745번: 오름세](https://www.acmicpc.net/problem/3745)

O(nlogn)에 LIS를 찾는 문제이다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_1365 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0

    private val lis = ArrayList<Int>()

    fun solve() {
        while(true) {
            val input = br.readLine()

            if(input == null) {
                print(sb)
                break
            }

            n = input.trim().toInt()

            lis.clear()
            lis.add(Int.MIN_VALUE)

            tokens = StringTokenizer(br.readLine())
            for(i in 0 ..< n) {
                val num = tokens.nextToken().toInt()

                if (num > lis[lis.size - 1]) //  lis에 저장된 마지막 수보다 클 경우 LIS에 추가, 길이 1 증가
                    lis.add(num)
                else {  //  num과 가장 가깝지만 더 큰 값 찾아서 대치시킴
                    val pos = binarySearch(num)

                    lis[pos] = num
                }
            }

            sb.append("${lis.size - 1}\n")
        }
    }

    //  크기 순으로 num 다음 수의 index 직전 index 반환
    private fun binarySearch(num : Int) : Int {
        var start = 0
        var end = lis.size - 1

        while(start < end) {
            val mid = (start + end) / 2

            if(lis[mid] < num)  //  num이 중간 수보다 더 클 경우 오른쪽에서 찾기
                start = mid + 1
            else    //  num이 중간 수보다 더 작거나 같을 경우
                end = mid
        }

        return end
    }
}

fun main() {
    BOJ_1365().solve()
}
```