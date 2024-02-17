# 24_02_18_daily_certification

```
[koreii] #49 데일리인증
20240218
알고리즘
- Greedy, PriorityQueue 문제 풀이
- Kotlin 커스텀 정렬 기준, PriorityQueue 우선순위 기준 설정 재정리
```

# Problem Solving (Algorithm & SQL)

**BOJ 9869 Milk Scheduling**

[9869번: Milk Scheduling](https://www.acmicpc.net/problem/9869)

이전에 풀었던 순회강연, 컵라면 문제처럼 해결할 수 있다.

[2109번: 순회강연](https://www.acmicpc.net/problem/2109)

[1781번: 컵라면](https://www.acmicpc.net/problem/1781)

2109 순회강연 때 풀이다.

![Untitled](24_02_18_daily_certification%20834e9243640f471695fd04a454b1c68e/Untitled.png)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_9869 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  소 개수
    private val cowList = ArrayList<Cow>()  //  소 목록
    private val pq = PriorityQueue<Int>()   //  젖 짤 수 있는 소 목록
    private var ans = 0 //  최대로 얻을 수 있는 우유 양

    fun solve() {
        n = br.readLine().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            cowList.add(Cow(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        cowList.sortBy {it.d }

        for(i in 0 ..< n) {
            pq.offer(cowList[i].g)

            if(cowList[i].d < pq.size)
                pq.poll()
        }

        while(pq.isNotEmpty())
            ans += pq.poll()

        println(ans)
    }

    private class Cow(val g : Int, val d : Int)
}

fun main() {
    BOJ_9869().solve()
}
```

## Kotlin 커스텀 정렬 방법, 우선순위 설정 방법

**BOJ 1202 보석 도둑 문제에서**

[1202번: 보석 도둑](https://www.acmicpc.net/problem/1202)

### **ArrayList의 커스텀 정렬 방법**

**sortBy, sortByDescending 사용**

보석의 무게 m으로만 정렬을 할 경우 다음과 같이 **sortBy**, **sortByDescending**을 사용할 수 있다.

```kotlin
private val gemList = ArrayList<Gem>()    //    보석 리스트

// 오름차순 정렬
gemList.sortBy{it.m}  // 보석의 무게 기준 오름차순 정렬
// 내림차순 정렬
gemList.sortByDescending{it.m}  // 보석의 무게 기준 내림차순 정렬
```

**sortWith 사용**

만약 보석의 무게를 기준으로 오름차순으로 정렬하되, 보석의 무게가 같을 때, 보석의 가치가 큰 순서대로 정렬해야 한다면 2개 이상의 기준으로 정렬 기준을 설정해야 한다. 

객체 정렬에서 정렬 기준에 포함되는 객체의 property가 두 개 이상일 때 comparator, lambda 식으로 구현할 수 있다.

```kotlin
// if(보석의 무게가 같을 경우) 보석의 가치가 큰 순서대로 정렬
// else 보석의 무게가 작은 순서대로 정렬 
gemList.sortWith{it1, it2 -> if(it1.m == it2.m) it2.v - it1.v else it1.m - it2.m}
```

### **PriorityQueu의 우선 순위 설정 방법**

마찬가지로 lambda 식으로 우선 순위를 설정한다.

```kotlin
// Gem의 v 기준으로 큰 것을 우선
private val pq = PriorityQueue<Gem>{it1, it2 -> it2.v - it1.v}
```

마찬가지로 2개 이상의 기준으로 우선 순위를 설정할 경우에도 ArrayList의 sortWith와 같은 방법을 사용한다,

```kotlin
private val pq = PriorityQueue<Gem>{it1, it2 -> if(it1.m == it2.m) it2.v - it1.v else it1.m - it2.m}
```