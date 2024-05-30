# 24_05_31_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 최소 통과 시간**

[https://www.codetree.ai/missions/8/problems/minimum-transit-time/description](https://www.codetree.ai/missions/8/problems/minimum-transit-time/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/minimum-transit-time/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0   //  물건 개수
    private var m = 0   //  통로 개수

    private lateinit var times : IntArray   //  각 통로별 통과에 필요한 시간
    private var maxTime = 0                 //  가장 오래 걸리는 통로의 통과 시간

    fun solve() {
        tokens = StringTokenizer(br.readLine())

        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        times = IntArray(m)

        repeat(m) {
            times[it] = br.readLine().toInt()
            maxTime = maxTime.coerceAtLeast(times[it])
        }

        println(parametricSearch())
    }

    private fun parametricSearch() : Long {
        var start = 0L
        var end = maxTime.toLong() * n  //  모든 물건이 가장 오래 걸리는 통로로 통과한다 가정했을 때 시간
        var ret = 0L

        while(start <= end) {
            val mid = (start + end) / 2 //  mid 시간 동안 n개의 물건이 통과 가능한지 확인
        
            var cnt = 0L //  mid 시간 동안 통과 가능한 물건 수

            for(i in 0 until m) {
                cnt += mid / times[i]   //  mid 시간 동안 i 통로로 통과 가능한 물건 개수
                if(cnt >= n.toLong())   //  모두 통과 가능할 경우
                    break
            }

            if(cnt >= n.toLong()) {   //  mid 동안 모두 통과 가능할 경우
                ret = mid             //  일단 mid 저장
                end = mid - 1         //  더 짧은 시간 동안 통과 가능한지 확인
            }
            else    //  mid 동안 모두 통과 불가능할 경우
                start = mid + 1       //  더 긴 시간 동안 통과 가능한지 확인
        }

        return ret
    }
}

fun main() {
    Main().solve()
}
```

**CO{)E TREE 겹치지 않는 선분위의 두 점**

[https://www.codetree.ai/missions/8/problems/two-points-of-the-line-that-don't-overlap/description](https://www.codetree.ai/missions/8/problems/two-points-of-the-line-that-don't-overlap/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/two-points-of-the-line-that-don't-overlap/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0   //  놓아야 할 점 개수
    private var m = 0   //  선분 개수

    private val lineSegments = ArrayList<LineSegment>() //  선분 리스트

    fun solve() {
        tokens = StringTokenizer(br.readLine())

        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val from = tokens.nextToken().toLong()
            val to = tokens.nextToken().toLong()

            lineSegments.add(LineSegment(from, to))
        }

        lineSegments.sort()

        println(parametricSearch())
    }

    private fun parametricSearch() : Long {
        var start = 0L
        var end = lineSegments[m - 1].to - lineSegments[0].from //  가장 앞의 선분의 시작점, 가장 뒤의 선분의 끝 점 사이 거리
        var ret = 0L    //  가장 가까운 두 점 사이의 거리의 최댓값

        while(start <= end) {
            val mid = (start + end) / 2 //  점 사이 최소 거리를 mid로 했을 때
            var cnt = 0L //  배치 가능한 점의 개수
            var ok = false

            var startPos = lineSegments[0].from //  첫 번째 선분의 가장 왼쪽 점에 점 하나 놓기
            var add = (lineSegments[0].to - startPos) / mid + 1 //  첫 번째 선분에 놓을 수 있는 점 개수
            var endPos = startPos + (add - 1) * mid //  첫 번째 선분에 놓인 가장 마지막 점 좌표

            cnt += add

            for(i in 1 .. m - 1) {  //  두 번째 선분부터 체크
                val line = lineSegments[i]  //  현재 선분

                startPos = endPos + mid //  다음 점 놓일 수 있는 시작 위치
                add = 0 //  현재 선분에 놓을 수 있는 점 개수

                if(startPos <= line.to) { //  시작 위치가 현재 선분의 가장 오른쪽 점보다 작거나 같아야 현재 선분에 하나라도 놓을 수 있음
                    if(startPos < line.from)    //  시작 위치가 현재 선분 왼쪽에 존재할 경우
                        startPos = line.from    //  현재 선분의 가장 왼쪽 점부터 놓기 시작
                    add = (line.to - startPos) / mid + 1
                    endPos = startPos + (add - 1) * mid
                }

                cnt += add

                if(cnt >= n) {  //  n개 이상의 점을 놓을 수 있을 경우
                    ok = true
                    break
                }
            }

            if(ok) {    //  mid 간격으로 n개 점을 놓을 수 있을 경우
                ret = mid
                start = mid + 1     //  간격 더 넓혀보기
            }
            else    //  mid 간격으로 n개 점을 놓을 수 없는 경우
                end = mid - 1   //  간격 더 좁혀보기
        }

        return ret
    }

    private class LineSegment(val from : Long, val to : Long) : Comparable<LineSegment> {
        override fun compareTo(other : LineSegment) : Int { //  선분 시작점 기준 오름차순
            return this.from.compareTo(other.from)
        }
    }
}

fun main() {
    Main().solve()
}
```