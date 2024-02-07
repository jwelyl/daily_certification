# 24_02_07_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 12014 주식**

[12014번: 주식](https://www.acmicpc.net/problem/12014)

LIS의 길이를 구하는 문제이다. 테스트케이스 개수 T가 최대 100이고, N이 최대 10^4이므로 DP를 이용한 O(N^2) 풀이는 아마 시간 초과가 날 것이다. 따라서 이분 탐색을 이용한 O(NlogN) 풀이를 고안해야 한다.

![lis.jpeg](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/lis.jpeg)

초기에 LIS 리스트에 더미 값 Int.MIN_VALUE를 넣고 시작한다. LIS에는 수가 오름차순으로 저장된다.

주어진 수열의 수를 하나씩 확인한다.

1.  LIS에 저장된 마지막 값보다 클 경우 
    
    **LIS에 추가한다. 이 경우 LIS의 길이가 1증가한다.**
    
2. LIS에 저장된 마지막 값보다 작을 경우
    
    **LIS에 저장된 수 중 해당 수보다 크거나 같은 최소 수를 찾는다. 이 때 이분탐색을 이용해 O(logN)에 찾을 수 있다. 찾은 수를 현재 수열의 수로 대체한다.**
    

수열의 모든 수를 확인한 후, 더미 값을 제외한 LIS의 길이 - 1이 정답이다.

**코드**

```kotlin
import java.io.*
import java.util.*

class BOJ_12014 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    private val sb = StringBuilder()
    
    private val lis = ArrayList<Int>()
    
    private var t = 0
    private var n = 0
    private var k = 0
    
    fun solve() {
        t = br.readLine().toInt()
        
        repeat(t) {
            sb.append("Case #${it + 1}\n")
            
            tokens = StringTokenizer(br.readLine())
            n = tokens.nextToken().toInt()
            k = tokens.nextToken().toInt()
        
            lis.clear()
            lis.add(Int.MIN_VALUE)
            
            tokens = StringTokenizer(br.readLine())
            for(i in 0 ..< n) {
                val num = tokens.nextToken().toInt()
                
                if(lis[lis.size - 1] < num)    //   기존에 가장 큰 값보다 더 큰 값이 들어올 경우 LIS 길이 1 증가
                    lis.add(num)
                else {    //    num보다 크거나 같은 수 중 가장 작은 수 대체 
                    val idx = binarySearch(num)
                    lis[idx] = num
                }
            }
        
            sb.append("${if(lis.size - 1 >= k) 1 else 0}\n")
        }
        
        print(sb)
    }
    
    private fun binarySearch(num : Int) : Int {
        var start = 0
        var end = lis.size - 1
        
        while(start < end) {
            val mid = (start + end) / 2
            
            if(lis[mid] >= num)     //    크거나 같은 수 찾았을 경우
                end = mid           //    일단 저장
            else                    //    작을 경우
                start = mid + 1     //    더 오른쪽에서 찾아야 함
        }
        
        return end
    }
}

fun main() {
    BOJ_12014().solve()
}
```