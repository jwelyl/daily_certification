# 24_04_06_daily_certification

```
[koreii] #96 데일리인증
알고리즘 & 코딩 테스트 대비
- 스택 문제 풀이
```

# Problem Solving (Algorithm & SQL)

**BOJ 6198 옥상 정원 꾸미기**

[6198번: 옥상 정원 꾸미기](https://www.acmicpc.net/problem/6198)

17298 오큰수 문제처럼 해결할 수 있다.

[17298번: 오큰수](https://www.acmicpc.net/problem/17298)

오른쪽 건물부터 시작하여 현재 건물의 오른쪽 건물 중 최초로 높이가 더 높거나 같은 건물의 위치를 파악하면, 현재 건물에서 볼 수 있는 건물 수를 구할 수 있다.

예제 1을 보며 확인하자.

![ex1.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1.jpeg)

builidings[i]가 i번째 건물의 높이이고, right[i]는 i번째 건물의 오른쪽 건물들 중 i번째 건물과 가장 가까운 곳에 위치하고, i번째 건물의 높이보다 높거나 같은 건물의 위치이다. 가장 오른쪽 건물은 오른쪽 건물이 없으므로 -1으로 설정한다. right 배열을 완성하면 정답을 구할 수 있다.

![ex1_1.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1_1.jpeg)

가장 오른쪽 건물의 오른쪽에는 건물이 없으므로 right를 -1로 설정하고 해당 건물 정보를 스택에 넣어준다. 스택에 넣을 때는 건물 높이와 위치를 같이 넣어준다.

![ex1_2.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1_2.jpeg)

오른쪽에서 두 번째 건물을 확인한다. 스택에 있는 건물들 중 현재 건물보다 높이가 낮은 건물들은 확인 가능하므로 제거한다. 스택에서 최초로 높이가 높거나 같은 건물이 나오면 해당 건물의 위치로 설정하고 그런 건물이 없으면 right 값을 n으로 설정한다.

![ex1_3.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1_3.jpeg)

오른쪽에서 세 번째 건물을 확인했을 때 스택에 해당 건물보다 높이가 높은 건물이 존재하므로 right 값을 해당 건물의 위치로 설정한다.

![ex1_4.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1_4.jpeg)

![ex1_5.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1_5.jpeg)

![ex1_6.jpeg](24_04_06_daily_certification%205376aa3eea4b4daf8e230932c3660339/ex1_6.jpeg)

같은 방법으로 right 배열을 완성시키면 정답을 구할 수 있다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_6198 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    
    private var n = 0
    
    private lateinit var buildings : IntArray    //    buildings[i] : i번째 건물의 높이
    private lateinit var right : IntArray //    right[i] : i번째 건물의 오른쪽 건물 중 최초로 i번째 건물 높이 이상인 건물의 index
    
    private val stack = Stack<IntArray>()
    private var ans = 0L
    
    fun solve() {
        n = br.readLine().toInt()
        
        buildings = IntArray(n)
        right = IntArray(n) { n }
        right[n - 1] = -1    //    가장 마지막 건물은 오른쪽 건물이 없음
        
        repeat(n) {
            buildings[it] = br.readLine().toInt()
        }
        
        stack.push(intArrayOf(buildings[n - 1], n - 1))
        
        for(i in n - 2 downTo 0) {
            val b = buildings[i]    //    현재 빌딩 높이
            val bIdx = i            //    현재 빌딩 index
            
            while(stack.isNotEmpty()) {
                val rb = stack.peek()[0]        //    오른쪽 빌딩 높이
                val rbIdx = stack.peek()[1]     //    오른쪽 빌딩 index
                
                if(rb >= b) {    //    오른쪽 빌딩의 높이가 현재 빌딩 이상일 경우
                    right[bIdx] = rbIdx    //    현재 빌딩에서 볼 수 있는 빌딩 수 결정됨
                    break    //    종료
                }
                
                stack.pop()    //    현재 빌딩에서 볼 수 있는 빌딩들은 스택에서 제거
            }
            
            stack.push(intArrayOf(b, bIdx))    //    현재 빌딩 스택에 넣기
        }
        
        for(i in 0 ..< n) {
            val idx = right[i]
            
            if(idx == -1)
                ans += 0
            else
                ans += (idx - i - 1)
        }
        
        println(ans)
    }
}

fun main() {
    BOJ_6198().solve()
}
```

사실 가장 오른쪽 건물을 따로 예외처리로 -1로 처리할 필요 없이 같은 로직으로 처리해도 정답이 나온다.

**개선된 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_6198 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    
    private var n = 0
    
    private lateinit var buildings : IntArray    //    buildings[i] : i번째 건물의 높이
    private lateinit var right : IntArray //    right[i] : i번째 건물의 오른쪽 건물 중 최초로 i번째 건물 높이 이상인 건물의 index
    
    private val stack = Stack<IntArray>()
    private var ans = 0L
    
    fun solve() {
        n = br.readLine().toInt()
        
        buildings = IntArray(n)
        right = IntArray(n) { n }
        
        repeat(n) {
            buildings[it] = br.readLine().toInt()
        }
        
        for(i in n - 1 downTo 0) {
            val b = buildings[i]    //    현재 빌딩 높이
            val bIdx = i            //    현재 빌딩 index
            
            while(stack.isNotEmpty()) {
                val rb = stack.peek()[0]        //    오른쪽 빌딩 높이
                val rbIdx = stack.peek()[1]     //    오른쪽 빌딩 index
                
                if(rb >= b) {    //    오른쪽 빌딩의 높이가 현재 빌딩 이상일 경우
                    right[bIdx] = rbIdx    //    현재 빌딩에서 볼 수 있는 빌딩 수 결정됨
                    break    //    종료
                }
                
                stack.pop()    //    현재 빌딩에서 볼 수 있는 빌딩들은 스택에서 제거
            }
            
            stack.push(intArrayOf(b, bIdx))    //    현재 빌딩 스택에 넣기
        }
        
        for(i in 0 ..< n) {
            val idx = right[i]

            ans += (idx - i - 1)
        }
        
        println(ans)
    }
}

fun main() {
    BOJ_6198().solve()
}
```