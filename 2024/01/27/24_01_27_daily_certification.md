# 24_01_27_daily_certification

```
[koreii] #27 데일리인증
20240127
알고리즘
- 1202 보석 도둑 복습 - Kotlin IDE 없이 풀어보기 & 난이도 기여
- Kotlin ArrayList, PriorityQueue 커스텀 정렬, 우선순위 설정 방법 복습
```

# Problem Solving (Algorithm & SQL)

**BOJ 1202 보석 도둑**

[1202번: 보석 도둑](https://www.acmicpc.net/problem/1202)

**ArrayList의 커스텀 정렬 방법**

**sortBy, sortByDescending 사용**

```kotlin
private val gemList = ArrayList<Gem>()    //    보석 리스트

// 오름차순 정렬
gemList.sortBy{it.m}  // 보석의 무게 기준 오름차순 정렬
// 내림차순 정렬
gemList.sortByDescending{it.m}  // 보석의 무게 기준 내림차순 정렬
```

**sortWith 사용**

객체 정렬에서 정렬 기준에 포함되는 객체의 property가 두 개 이상일 때 comparator, lambda 식으로 구현할 수 있다.

```kotlin
// 보석의 무게 기준 오름차순 정렬
gemList.sortWith{it1, it2 -> it1.m - it2.m}
```

**PriorityQueu의 우선 순위 설정 방법**

마찬가지로 lambda 식으로 우선 순위를 설정한다.

```kotlin
// Gem의 v 기준으로 큰 것을 우선
private val pq = PriorityQueue<Gem>{it1, it2 -> it2.v - it1.v}
```

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1202 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    보석 개수
    private var k = 0    //    가방 개수

    private val gemList = ArrayList<Gem>()    //    보석 리스트
    private val bagList = ArrayList<Int>()    //    가방에 담을 수 있는 무게 리스트

    private val pq = PriorityQueue<Gem>{ it1, it2 -> it2.v - it1.v}    //    현재 가방에 담을 수 있는 보석 리스트, 가치가 큰 순으로 꺼냄
    private var ans = 0    //    가방에 담을 수 있는 보석들의 가치 합의 최대

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            gemList.add(Gem(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        repeat(k) {
            bagList.add(br.readLine().toInt())
        }

        gemList.sortBy{it.m}    //    보석은 무게가 가벼운 순으로 정렬
        bagList.sort()          //    가방도 담을 수 있는 무게가 가벼운 순으로 정렬

        var gemIdx = 0           //    보석 index

        for(bag in bagList) {    //    현재 가방에 담을 수 있는 무게 bag에 대하여
            while(gemIdx < n && gemList[gemIdx].m <= bag) {    //    gemIdx 보석을 담을 수 있으면
                pq.offer(gemList[gemIdx])     //    gemIdx 보석을 담음
                gemIdx++
            }

            if(pq.isNotEmpty())
                ans += pq.poll().v    //    현재 가방에 담을 수 있는 보석 중 가장 가치가 큰 것을 담음
            //    pq에 남은 나머지 보석들은 자연스럽게 다음 가방에 담을 수 있는 보석이 됨
        }

        println(ans)
    }

    //    보석 무게 m, 보석 가치 v
    private class Gem(val m : Int, val v : Int)
}

fun main() {
    BOJ_1202().solve()
}
```