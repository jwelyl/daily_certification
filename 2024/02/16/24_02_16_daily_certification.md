# 24_02_16_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 23294 웹 브라우저 1**

[23294번: 웹 브라우저 1](https://www.acmicpc.net/problem/23294)

deque의 pollFirst, pollLast, offerFirst, offerLast를 헷갈려서는 안되겠다. 그리고 가장 최근 방문한 페이지의 의미를 좀 더 제대로 파악하고 도전해봐야겠다.

**틀린 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_23294 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  접속 가능한 웹 페이지 수
    private var q = 0   //  사용자 수행 작업 개수
    private var c = 0   //  최대 캐시 용량

    private lateinit var cap : IntArray //  각 웹 페이지 용량

    private var curPage = -1    //  현재 페이지
    private var curSize = 0     //  현재 페이지 캐시 용량
    private var backwardDeque = LinkedList<Int>()  //  뒤로 가기 공간
    private var backwardSize = 0    //  뒤로 가기 공간 캐시 용량
    private val forwardDeque = LinkedList<Int>()   //  앞으로 가기 공간
    private var forwardSize = 0     //  앞으로 가기 공간 캐시 용량

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        q = tokens.nextToken().toInt()
        c = tokens.nextToken().toInt()

        cap = IntArray(n + 1)

        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. n)
            cap[i] = tokens.nextToken().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())

            val cmd = tokens.nextToken()[0]

            if(cmd == 'B') {    //  뒤로 가기일 경우
                if(backwardDeque.isNotEmpty()) {
                    forwardDeque.offerLast(curPage) // 현재 페이지를 앞으로 가기 공간에 저장
                    forwardSize += cap[curPage] //  앞으로 가기 공간 캐시 용량 증가

                    curPage = backwardDeque.pollLast() //  가장 최근 페이지에 접속 후 그 페이지는 뒤로 가기 공간에서 삭제
                    backwardSize -= cap[curPage]        //  뒤로 가기 공간 캐시 용량 감소

                    curSize = cap[curPage]  //  현재 페이지 캐시 용량 갱신
                }
            }
            else if(cmd == 'F') {   //  앞으로 가기일 경우
                if(forwardDeque.isNotEmpty()) {
                    backwardDeque.offerLast(curPage)    //  현재 페이지를 뒤로 가기 공간에 저장
                    backwardSize += cap[curPage]    //  뒤로 가기 공간 캐시 용량 증가

                    curPage = forwardDeque.pollLast()  //  가장 최근 페이지에 접속 후 그 페이지는 앞으로 가기 공간에서 삭제
                    forwardSize -= cap[curPage]         //  앞으로 가기 공간 캐시 용량 감소

                    curSize = cap[curPage]  //  현재 페이지 캐시 용량 갱신
                }
            }
            else if(cmd == 'A') {   //  페이지 방문
                val page = tokens.nextToken().toInt()   //  방문할 페이지

                forwardDeque.pollFirst()    //  앞으로 가기 공간 비우기
                forwardSize = 0             //  앞으로 가기 공간 캐시 용량 0

                if(curPage != -1) { //  최초 방문이 아닐 경우
                    backwardDeque.offer(curPage)    //  가장 최근에 방문한 페이지를 뒤로 가기 공간에 넣기
                    backwardSize += cap[curPage]    //  뒤로 가기 공간 캐시 용량 증가
                }

                curPage = page          //  현재 페이지를 방문할 페이지로 변경
                curSize = cap[page]     //  현재 페이지 캐시 용량 증가

                while(totalUsedCap() > c) {    //  전체 캐시 용량이 최대 캐시 용량을 초과할 경우
                    val out = backwardDeque.pollFirst()  //  뒤로 가기 공간에서 가장 오래 전에 방문한 페이지 제거
                    backwardSize -= cap[out]    //  뒤로 가기 공간 캐시 용량 감소
                }
            }
            else {  //  압축 실행
                val tmp = LinkedList<Int>()
                backwardSize = 0

                while(backwardDeque.isNotEmpty()) {
                    val page = backwardDeque.pollFirst()

                    if(tmp.isEmpty() || tmp.peekLast() != page) {
                        tmp.offerLast(page)
                        backwardSize += cap[page]
                    }
                }

                backwardDeque = tmp
            }
        }

        sb.append("$curPage\n")
        if(backwardDeque.isNotEmpty()) {
            while(backwardDeque.isNotEmpty())
                sb.append("${backwardDeque.pollLast()} ")
        } else sb.append(-1)
        sb.append("\n")
        if(forwardDeque.isNotEmpty()) {
            while(forwardDeque.isNotEmpty())
                sb.append("${forwardDeque.pollLast()} ")
        } else sb.append(-1)
        sb.append("\n")

        print(sb)
    }

    private fun totalUsedCap() : Int {
        return forwardSize + curSize + backwardSize
    }
}

fun main() {
    BOJ_23294().solve()
}
```
