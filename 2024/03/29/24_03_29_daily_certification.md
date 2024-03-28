# 24_03_29_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 20917 사회적 거리 두기**

[20917번: 사회적 거리 두기](https://www.acmicpc.net/problem/20917)

일전에 풀었던 공유기 설치 문제와 유사하게 해결할 수 있다.

[2110번: 공유기 설치](https://www.acmicpc.net/problem/2110)

**코드 (Bruteforcing)**

```kotlin
import java.util.*
import java.io.*

class BOJ_20917 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    private val sb = StringBuilder()

    private var tc = 0    //    테스트 케이스 개수
    private var n = 0     //    콘센트 수
    private var s = 0     //    선택할 위치 수

    private lateinit var pos : IntArray    //    콘센트 위치

    private var start = 0
    private var end = 0

    fun solve() {
        tc = br.readLine().toInt()

        repeat(tc) {
            tokens = StringTokenizer(br.readLine())
            n = tokens.nextToken().toInt()
            s = tokens.nextToken().toInt()

            pos = IntArray(n)

            tokens = StringTokenizer(br.readLine())
            for(i in 0 ..< n)
                pos[i] = tokens.nextToken().toInt()

            pos.sort()    //    오름차순으로 정렬

            sb.append("${parametricSearch()}\n")
        }

        print(sb)
    }

    //    s명을 배치할 때 가장 가까운 두 사람의 최대 거리 반환
    private fun parametricSearch() : Int {
        var ret = Int.MAX_VALUE

        start = 0   //  가장 가까운 두 사람의 최대 거리가 0
        end = pos[n - 1] - pos[0]   //  가장 가까운 두 사람이 양 끝에 있을 때

        while(start <= end) {
            val mid = (start + end) / 2    //    mid가 가장 가까운 거리라고 가정, 즉 인접한 두 사람은 반드시 mid 이상 떨어져야 함

            if(isOk(mid)) {    //    어느 인접한 두 사람의 거리가 mid 이상일 경우
                ret = mid      //    일단 최소 mid만큼은 떨어뜨려놓을 수 있음
                start = mid + 1    //    더 떨어뜨려놓아보기
            } else end = mid - 1   //    어느 인접한 두 사람의 거리가 mid 미만일 경우, mid가 가장 가까운 거리가 될 수 없음, 더 줄여야 함
        }

        return ret
    }

    //    s명의 사람이 최소 mid만큼 떨어져 있을 수 있으면 true 반환
    private fun isOk(mid : Int) : Boolean {
        var cnt = 1    //    첫 번째 사람 1명 배치 가능
        var curPos = pos[0]    //    첫 번째 사람 가장 왼쪽 배치

        for(i in 1 ..< n) {
            val nextPos = pos[i]    //    다음 사람 배치 위치

            if(nextPos - curPos >= mid) {    //    다음 사람을 현재 사람과 mid 이상의 거리로 배치할 수 있으면
                cnt++    //    배치할 수 있는 사람 1명 증가
                curPos = nextPos    //    다음 사람을 배치

                if(cnt == s)    //    s명을 모두 배치했다면
                    return true
            }
        }

        return false    //    s명을 배치할 수 없음
    }
}

fun main() {
    BOJ_20917().solve()
}
```