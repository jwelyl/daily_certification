# 24_01_06_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 14570 나무 위의 구슬**

[14570번: 나무 위의 구슬](https://www.acmicpc.net/problem/14570)

트리 **+ 깊이 우선 탐색**

떨어뜨리려는 구슬의 개수가 최대 10^18개라 일일이 떨어뜨려볼 수는 없다.

k번째 구슬을 특정 서브트리의 루트에 놓았을 때

k가 홀수일 경우 k / 2 개의 구슬과 k번째 구슬이 왼쪽 서브트리로 이동하고,

k가 짝수일 경우 k번째 구슬을 포함한 k / 2 개의 구슬이 오른쪽 서브트리로 이동한다.

Bruteforce가 불가능할 경우 작은 예시를 통해 규칙을 발견하는 시도를 해봐야 한다.

**코드**

```kotlin
import java.util.*
import java.io.*

const val ROOT = 1

class BOJ_14570 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  이진 트리 노드 개수
    private lateinit var tree : Array<Node> //  이진 트리
    private var k = 0L       //  떨어뜨릴 구슬 개수

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n + 1) { Node() }
        repeat(n) {
            tokens = StringTokenizer(br.readLine())

            tree[it + 1].left = tokens.nextToken().toInt()
            tree[it + 1].right = tokens.nextToken().toInt()
        }

        k = br.readLine().toLong()

        dfs(ROOT, k)
    }

    //  k번째 구슬을 cur을 루트로 하는 서브트리에 놓음
    private fun dfs(cur : Int, k : Long) {
        val leftChild = tree[cur].left
        val rightChild = tree[cur].right

        if(leftChild == -1 && rightChild == -1) {   //  단말 노드에 도착함
            println(cur)
            return
        }

        if(leftChild != -1 && rightChild != -1) {   //  자식이 둘 다 있을 경우
            if(k % 2 == 1L)   //  홀수번째 구슬일 경우 왼쪽 서브트리에 놓아야 함
                dfs(leftChild, k / 2 + 1)   //  왼쪽 서브트리에 절반(k / 2) 놓고 오른쪽 서브트리보다 1개 더(이게 k번째 구슬) 놓아야 함
            else
                dfs(rightChild, k / 2)  //  오른쪽 서브트리에 절반 놓아야 함(k번째 구슬 포함)
        }
        else if(leftChild != -1)    //  왼쪽 자식만 있을 경우
            dfs(leftChild, k)   //  왼쪽 서브트리로 k개 구슬 다 이동
        else                        //  오른쪽 자식만 있을 경우
            dfs(rightChild, k)  //  오른쪽 서브트리로 k개 구슬 다 이동
    }

    private class Node {
        var left = -1
        var right = -1
    }
}

fun main() {
    BOJ_14570().solve()
}
```