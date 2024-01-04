# 24_01_04_daily_certification

# Computer Network

## Socket

OS가 Application과 Network (Transport Layer) 사이에 제공하는 Interface

- TCP Socket
- UDP Socket
- Sockets API
- Socket Server vs Client

## Multiplexing vs Demultiplexing

![IMG_A1BEF1DAA789-1.jpeg](24_01_04_daily_certification%203e21439f6c6243a5a6eba34065906f32/IMG_A1BEF1DAA789-1.jpeg)

- TCP Demux (보충 필요)
- UDP Demux (보충 필요)

# Problem Solving (Algorithm & SQL)

**BOJ 2250 트리의 높이와 너비**

[2250번: 트리의 높이와 너비](https://www.acmicpc.net/problem/2250)

트리, 중위 순회 or 깊이 우선 탐색

트리 노드 정보를 받는 과정에서 어떤 노드의 자식 노드도 되지 않는 노드를 루트 노드로 정한다.

루트 노드에서부터 중위 순회를 시작한다.

왼쪽 서브트리 탐색

현재 노드의 위치는 왼쪽 서브트리에서 가장 오른쪽에 있는 노드의 위치 + 1 이 된다.

오른쪽 서브트리 탐색

오른쪽 서브트리의 가장 왼쪽 노드의 위치는 현재 노드의 위치 + 1 이 된다.

현재 노드가 리프 노드일 때는 주어진 위치가 현재 노드의 위치가 된다.

모든 노드의 위치를 구한 후 level별로 확인하면서 가장 큰 너비를 찾으면 된다.  

**코드**

```kotlin
import java.util.*
import java.io.*

const val NONE = -1

class BOJ_2250 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  트리 노드 개수
    private lateinit var binTree : Array<BinTreeNode>
    private var root = 0

    fun solve() {
        n = br.readLine().toInt()

        binTree = Array(n + 1) { BinTreeNode() }

        repeat(n) {
            tokens = StringTokenizer(br.readLine())

            val num = tokens.nextToken().toInt()
            val leftChild = tokens.nextToken().toInt()
            val rightChild = tokens.nextToken().toInt()

            binTree[num].leftChild = leftChild
            binTree[num].rightChild = rightChild

            if(leftChild != NONE)
                binTree[leftChild].parent = num
            if(rightChild != NONE)
                binTree[rightChild].parent = num
        }

        for(i in 1 .. n) {
            if (binTree[i].parent == NONE) {
                root = i
                break
            }
        }

        dfs(root, 1, 1)

        Arrays.sort(binTree)

        var maxLevel = 0
        var maxWidth = 0
        var idx = 1

        while(true) {
            if(idx > n)
                break

            val curLevel = binTree[idx].level
            val curPos = binTree[idx].pos
            var nextPos = -1

            for(j in idx + 1 .. n) {
                if(binTree[j].level == curLevel) {
                    nextPos = binTree[j].pos
                    idx = j + 1
                }
            }

            if(nextPos == -1) {
                if(maxWidth < 1) {
                    maxWidth = 1
                    maxLevel = curLevel
                }

                idx++
            }
            else {
                if(maxWidth < nextPos - curPos + 1) {
                    maxWidth = nextPos - curPos + 1
                    maxLevel = curLevel
                }
            }
        }

        println("$maxLevel $maxWidth")
    }

    private fun dfs(num : Int, pos : Int, level : Int) {
        binTree[num].level = level

        if(binTree[num].leftChild == NONE && binTree[num].rightChild == NONE) { //  리프 노드일 경우
            binTree[num].pos = pos
            binTree[num].minPos = pos
            binTree[num].maxPos = pos
            return
        }

        if(binTree[num].leftChild != NONE) {    //  왼쪽 자식이 있을 경우
            dfs(binTree[num].leftChild, pos, level + 1) //  왼쪽 자식 탐방
            binTree[num].pos = binTree[binTree[num].leftChild].maxPos + 1   //  현재 노드의 열 위치는 왼쪽 자식 노드 서브트리의 가장 오른쪽 노드 열 위치 + 1
            binTree[num].minPos = binTree[binTree[num].leftChild].minPos    //  현재 노드 서브트리의 가장 왼쪽 위치는 왼쪽 자식 노드 서브트리의 가장 왼쪽 노드 열 위치
        }
        else {   //  왼쪽 자식이 없을 경우
            binTree[num].pos = pos      //  현재 노드 위치는 주어진 위치
            binTree[num].minPos = pos   //  현재 노드 서브트리의 가장 왼쪽 위치는 현재 노드 위치
        }

        if(binTree[num].rightChild != NONE) {   //  오른쪽 자식이 있을 경우
            dfs(binTree[num].rightChild, binTree[num].pos + 1, level + 1)   //  오른쪽 자식 탐방
            binTree[num].maxPos = binTree[binTree[num].rightChild].maxPos   //  현재 노드 서브트리의 가장 오른쪽 위치는 오른쪽 자식 서브트리의 가장 오른쪽 노드 열 위치
        }
        else //  오른쪽 자식이 없을 경우
            binTree[num].maxPos = binTree[num].pos  //  현재 노드 서브트리의 가장 오른쪽 위치는 현재 노드 위치
    }

    private class BinTreeNode : Comparable<BinTreeNode> {
        var level = 0               //  현재 이진트리 노드의 레벨
        var pos = 0                 //  현재 이진트리 노드의 열 위치
        var leftChild = 0           //  현재 이진트리 노드의 왼쪽 자식 노드
        var rightChild = 0          //  현재 이진트리 노드의 오른쪽 자식 노드
        var parent = NONE           //  현재 이진트리 노드의 부모 노드
        var minPos = 0              //  현재 이진트리 노드를 루트로 하는 서브 트리의 노드들 중 가장 왼쪽 노드의 열 위치
        var maxPos = 0              //  현재 이진트리 노드를 루트로 하는 서브 트리의 노드들 중 가장 오른쪽 노드의 열 위치

        override fun compareTo(other: BinTreeNode): Int {
            var ret = this.level.compareTo(other.level)

            if(ret == 0)
                ret = this.pos.compareTo(other.pos)

            return ret
        }
    }
}

fun main() {
    BOJ_2250().solve()
}
```