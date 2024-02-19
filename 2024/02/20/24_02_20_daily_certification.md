# 24_02_20_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1379 강의실 2**

[1379번: 강의실 2](https://www.acmicpc.net/problem/1379)

우선순위큐를 두 개 사용해야 하는 문제였다.

pq1에는 처음 입력받은 강의들을 모두 저장해둔다. 강의실을 배정받아야 하는 강의다. 강의실을 배정받는 순서대로 저장하며, 배정 순서는 빨리 시작하는 강의, 그 중에서도 빨리 끝나는 강의이다.

pq1에는 Lecture 객체가 {강의 번호, 시작 시간, 종료 시간}로 저장된다.

pq2에는 강의실이 배정된 강의들이 저장된다. 만약 하나의 강의실에 배정된 강의가 여러개일 경우 해당 강의실에 마지막으로 배정된 강의만 저장된다. 따라서 최종적으로 pq2에 저장되는 강의 개수는 배정될 강의실 수와 같다.

p2에는 강의실이 배정된 강의가 [종료 시간, 배정된 강의실 번호]로 저장된다.

pq1에서 강의를 하나씩 꺼내어(꺼낸 강의를 next이라 하자.) pq2에 넣는다. pq2에 강의가 이미 존재할 경우 pq2에 존재하는 강의 중 가장 빨리 끝나는 강의 prev와 비교해야 한다. 

next의 시작 시간이 prev의 끝나는 시간 이후일 경우 prev와 next의 시간이 겹치지 않으므로 prev와 같은 강의실에 next를 배정할 수 있다. 띠리서 prev를 제거하고 대신 next를 넣을 수 있다.

nex의 시작 시간이 prev의 끝나는 시간 이전일 경우 prev와 next의 시간이 겹치므로 prev와 같은 강의실에 next를 배치할 수 없다. 따라서 새로운 강의실을 배치하고 next를 넣어야 한다.

next는 어쨋든 pq2에 저장된다. 후에 다른 강의와 같은 강의실을 사용할 수 있을 경우 빠질 순 있다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1379 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  강의 개수

    //  강의 시작 시간이 빠른 순으로, 시작 시간이 같으면 종료 시간이 빠른 순으로
    private val pq1 = PriorityQueue<Lecture> { it1, it2 -> if(it1.from == it2.from) it1.to - it2.to else it1.from - it2.from }
    //  강의 종료 시간이 빠른 순으로, 종료 시간이 같으면 강의실 번호가 작은 순으로
    private val pq2 = PriorityQueue<IntArray> { it1, it2 -> if(it1[0] == it2[0]) it1[1] - it2[1] else it1[0] - it2[0] }

    private var ans = 0 //  배정될 강의실 개수
    //  roomNum[i] : i번째 강의에 배정될 강의실 번호
    private lateinit var roomNum : IntArray

    fun solve() {
        n = br.readLine().toInt()

        roomNum = IntArray(n + 1)

        //  강의들을 pq1에 저장
        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            pq1.offer(Lecture(tokens.nextToken().toInt(), tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        var lecture = pq1.poll()    //  가장 빨리 시작하는 강의(그 중에서도 가장 빨리 끝나는 강의)를 꺼내서 1번 강의실에 배정
        pq2.offer(intArrayOf(lecture.to, 1))    //  pq2에 해당 강의 1번 강의실에 배정
        roomNum[lecture.num] = 1    //  해당 강의는 1번 강의실에 배정됨

        while(pq1.isNotEmpty()) {   //  모든 강의에 강의실 배정할 때 까지
            lecture = pq1.poll()    //  배정할 강의를 꺼냄
            var idx = -1    //  강의실 배정 안됐으면 -1

            if(pq2.peek()[0] <= lecture.from) { //  강의실 배정된, 가장 빨리 끝나는 강의의 끝나는 시간보다 배정할 강의의 강의 시작 시간이 뒤일 경우
                //  강의실 배정된, 가장 빨리 끝나는 강의가 끝나면 해당 강의실에 배정할 강의 lecture를 배정할 수 있음
                val tmp = pq2.poll()    //  이미 배정된 강의를 제거함(대신 이번에 배정될 강의가 현재 강의실을 나타낼 것임)
                idx = tmp[1]    //  현재 강의실 번호를 배정함
            }

            if(idx == -1)   //  이미 배정된 강의 다음에 강의를 배정할 수 없을 경우
                idx = pq2.size + 1  //  강의실 하나 추가

            roomNum[lecture.num] = idx  //  강의실 배정할 강의의 강의실 번호
            pq2.offer(intArrayOf(lecture.to, idx))  //  강의실 배정 후 pq2에 넣기
        }

        //  pq2에 들어있는 강의 수가 강의실 개수임
        sb.append("${pq2.size}\n")
        for(i in 1 .. n)
            sb.append("${roomNum[i]}\n")

        print(sb)
    }

    private class Lecture(val num : Int, val from : Int, val to : Int)
}

fun main() {
    BOJ_1379().solve()
}
```

정답 코드를 보고 나면 idea도 쉽게 이해되지만, idea를 먼저 떠올리는 건 경험이 부족하면 정말 어려울 것 같다. idea를 겨우 떠올렸어도 비슷한 로직을 구현해본 경험이 없으면 idea를 구현하기도 어려울 것 같다.

## Recap. Kotlin 커스텀 정렬 방법, 우선순위 설정 방법

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