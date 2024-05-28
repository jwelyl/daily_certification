# 24_05_28_daily_certification

# Side Project

### Side Project ERD CLOUD

**팀 페이지**

[ERDCloud](https://www.erdcloud.com/team/KnojjqmjLmZMWR2gL)

**ERD**

[Side Project](https://www.erdcloud.com/d/oXWj5rnsrJpDmDrWF)

### ERD CLOUD 사용법

[☁️ ERD CLOUD - ERD 다이어그램을 온라인에서 그려보자](https://inpa.tistory.com/entry/ERD-CLOUD-☁️-ERD-다이어그램을-온라인에서-그려보자)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 컴퓨터와 함께하는 숫자 게임 2**

[https://www.codetree.ai/missions/8/problems/play-number-game-with-computer-2/description](https://www.codetree.ai/missions/8/problems/play-number-game-with-computer-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/play-number-game-with-computer-2/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var m = 0L
    private var a = 0L
    private var b = 0L

    private var min = 0L        //  최소 찾는 횟수
    private var max = 0L     //  최대 찾는 횟수

    fun solve() {
        m = br.readLine().toLong()

        min = m + 1

        tokens = StringTokenizer(br.readLine())
        a = tokens.nextToken().toLong()
        b = tokens.nextToken().toLong()

        for(target in a .. b) {
            val cnt = binarySearch(target)

            min = min.coerceAtMost(cnt.toLong())
            max = max.coerceAtLeast(cnt.toLong())
        }

        println("$min $max")
    }

    //  정석적인 이진탐색으로 target을 찾기 위한 탐색 횟수
    private fun binarySearch(target : Long) : Int {
        var start = 1L
        var end = m
        var cnt = 1

        while(start <= end) {
            val mid = (start + end) / 2

            if(mid == target.toLong())
                break
            else if(mid < target)
                start = mid + 1
            else
                end = mid - 1

            cnt++
        }

        return cnt
    }
}

fun main() {
    Main().solve()
}
```

**CO{)E TREE 자연수 n개의 합**

[https://www.codetree.ai/missions/8/problems/sum-of-n-natural-numbers/description](https://www.codetree.ai/missions/8/problems/sum-of-n-natural-numbers/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-n-natural-numbers/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))

    private var s = 0L

    fun solve() {
        s = br.readLine().toLong()

        println(parametricSearch())
    }

    private fun parametricSearch() : Long {
        var start = 1L
        var end = 10_000_000_000

        var ans = 0L

        while(start <= end) {
            val mid = (start + end) / 2

            if(mid * (mid + 1) / 2 <= s) {  //  1부터 mid까지의 합이 s 이하일 경우
                ans = mid           //  mid는 가능한 n 값임
                start = mid + 1     //  mid 더 키워보기
            }
            else end = mid - 1      //  mid 더 줄여야 함
        }

        return ans
    }
}

fun main() {
    Main().solve()
}
```

**CO{)E TREE 정수 분배하기**

[https://www.codetree.ai/missions/8/problems/distributing-integers/description](https://www.codetree.ai/missions/8/problems/distributing-integers/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/distributing-integers/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0

    private lateinit var nums : IntArray
    private var start = 1
    private var end = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        nums = IntArray(n)
        repeat(n) {
            nums[it] = br.readLine().toInt()

            end = end.coerceAtLeast(nums[it])
        }

        println(parametricSearch())
    }

    private fun parametricSearch() : Int {
        var ans = 0

        while(start <= end) {
            val mid = (start + end) / 2 //  나누는 정수 크기
            val cnt = divide(mid)       //  n개의 수를 mid로 나누었을 때 만들 수 있는 mid 크기의 정수 개수

            if(cnt < m) //  m개 이상 만들지 못할 경우
                end = mid - 1   //  나누는 정수 크기 줄여야 함
            else {  //  m개 이상 만들 수 있는 경우
                ans = mid           //  일단 mid 저장
                start = mid + 1     //  더 큰 수로 나눌 수 있나 체크
            }
        }

        return ans
    }

    private fun divide(divisor : Int) : Int {
        var ret = 0

        for(num in nums)
            ret += num / divisor

        return ret
    }
}

fun main() {
    Main().solve()
}
```