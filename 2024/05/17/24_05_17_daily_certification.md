# 24_05_17_daily_certification (1)

```
[koreii] #138 데일리인증
2024517
사이드 프로젝트
- 기능 명세에 따른 와이어프레임 제작 중
알고리즘 & 코딩 테스트 대비
- 코드트리 PriorityQueue, Doubly Linked List, HashMap
- Kotlin sortedBy, sortedWith, sortBy, sortWith 학습
```

# Side Project

[https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/와이어프레임?node-id=0-1&t=kZti5PkOLcDyOCoF-0](https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/%EC%99%80%EC%9D%B4%EC%96%B4%ED%94%84%EB%A0%88%EC%9E%84?node-id=0-1&t=kZti5PkOLcDyOCoF-0)

[https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/%EC%99%80%EC%9D%B4%EC%96%B4%ED%94%84%EB%A0%88%EC%9E%84?node-id=0-1&t=kZti5PkOLcDyOCoF-0](https://www.figma.com/design/OqqxvbgsCXDJKCYdUpMalJ/%EC%99%80%EC%9D%B4%EC%96%B4%ED%94%84%EB%A0%88%EC%9E%84?node-id=0-1&t=kZti5PkOLcDyOCoF-0)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 연결리스트 2**

[https://www.codetree.ai/missions/8/problems/linked-list2/introduction](https://www.codetree.ai/missions/8/problems/linked-list2/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/linked-list2/introduction)

**코드** 

```kotlin
import java.util.*
import java.io.*
import java.lang.StringBuilder
import kotlin.collections.ArrayList

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer
    private val sb = StringBuilder()

    private var n = 0   //  노드 개수
    private var q = 0   //  질의 개수

    private val nodeMap = HashMap<Int, Node>()  //  key : 노드 번호, value : 노드

    fun solve() {
        n = br.readLine().toInt()
        q = br.readLine().toInt()

        for(i in 1 .. n)
            nodeMap[i] = Node(i)

        repeat(q) {
            tokens = StringTokenizer(br.readLine())

            when(val cmd = tokens.nextToken().toInt()) {
                1 -> {
                    val outNum = tokens.nextToken().toInt() //  꺼낼 노드
                    poll(outNum)
                }
                2, 3 -> {
                    val posNum = tokens.nextToken().toInt()        //  삽입될 노드 번호
                    val pushNum = tokens.nextToken().toInt()    //  삽입할 노드 번호

                    if(cmd == 2)
                        pushFront(posNum, pushNum)
                    else
                        pushBack(posNum, pushNum)
                }
                4 -> {
                    val posNum = tokens.nextToken().toInt() //  앞뒤 확인할 노드 번호
                    val posNode = nodeMap[posNum]

                    val prevNum = if(posNode!!.prev == null) 0 else posNode.prev!!.num    //  앞노드 번호
                    val nextNum = if(posNode.next == null) 0 else posNode.next!!.num      //  뒷노드 번호

                    sb.append("$prevNum $nextNum\n")
                }
            }
        }

        for(i in 1 .. n) {
            val curNode = nodeMap[i]!!

            sb.append(if(curNode.next == null) 0 else curNode.next!!.num).append(" ")
        }

        print(sb)
    }

    //  outNum에 해당하는 노드를 DLL에서 꺼냄
    private fun poll(outNum : Int) {
        val outNode = nodeMap[outNum]

        val prevNode = outNode!!.prev
        val nextNode = outNode.next

        if(prevNode != null)    //  이전 노드가 존재할 경우
            prevNode.next = nextNode    //  이전 노드의 다음 노드는 다음 노드
        if(nextNode != null)    //  다음 노드가 존재할 경우
            nextNode.prev = prevNode    //  다음 노드의 이전 노드는 이전 노드
        outNode.prev = null
        outNode.next = null
    }

    //  posNum에 해당하는 노드 앞에 pushNum에 해당하는 노드를 삽입함
    private fun pushFront(posNum : Int, pushNum : Int) {
        val posNode = nodeMap[posNum]
        val pushNode = nodeMap[pushNum]
        val prevNode = posNode!!.prev

        if(prevNode == null) {
            posNode.prev = pushNode
            pushNode!!.next = posNode
        }
        else {
            pushNode!!.prev = prevNode
            prevNode.next = pushNode
            pushNode.next = posNode
            posNode.prev = pushNode
        }
    }

    //  posNum에 해당하는 노드 뒤에 pushNum에 해당하는 노드를 삽입함
    private fun pushBack(posNum : Int, pushNum : Int) {
        val posNode = nodeMap[posNum]
        val pushNode = nodeMap[pushNum]
        val nextNode = posNode!!.next

        if(nextNode == null) {
            posNode.next = pushNode
            pushNode!!.prev = posNode
        }
        else {
            pushNode!!.next = nextNode
            nextNode.prev = pushNode
            pushNode.prev = posNode
            posNode.next = pushNode
        }
    }

    private class Node(val num : Int) {
        var prev : Node? = null     //  이전 노드
        var next : Node? = null     //  다음 노드
    }
}

fun main() {
    Main().solve()
}
```