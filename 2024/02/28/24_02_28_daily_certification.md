# 24_02_28_daily_certification

```
[koreii] #59 데일리인증
20240228
알고리즘
- HTML 태그 파싱 & 스택 문제 풀이 + 난이도 기여
- 로직 설계 후 코드 작성하기
```

# Problem Solving (Algorithm & SQL)

**BOJ 5076 Web Pages**

[5076번: Web Pages](https://www.acmicpc.net/problem/5076)

<br />과 같은 Self-closing 태그도 어떻게 처리해야 할 지 까다로웠고, <a></a> 태그 안에는 링크와 같은 내용이 들어갈 수 있어서 접근하는데 조금 애먹었었다.

주요 로직은 이렇다.

opening 태그를 저장하는 스택을 놓고, opening 태그는 스택에 저장하고 closing 태그가 나오면 스택에 가장 마지막에 저장된 opening 태그를 확인해서 closing 태그와 일치하면 스택에서 제거한다.

self-closing 태그는 스택에 넣지 않는다.

최종적으로 스택이 비어있으면 legal하다.

예제 2를 그림으로 나타낸 것이다.

![ex2.jpeg](24_02_28_daily_certification%20ab8afc076a874daa89def694abc30668/ex2.jpeg)

좀 더 편한 구현을 위해 입력 문자열에서 태그 내부에 있는 부분만 따로 저장하는 정제된 문자열 refinedInput을 만들었다. ‘<’가 나온 이후 나오는 문자들을 저장하고 ‘>’가 나오면 ‘>’까지 저장 후 더 이상 저장을 안한다. ‘<’가 나오면 다시 저장한다.

그렇게 정제된 refinedInput을 ‘>’로 파싱하면 태그명과 ‘/’만 남는데, ‘/’를 이용해 태그 종류를 구분할 수 있다.

예제 5를 그림으로 나타낸 것이다.

![ex5.jpeg](24_02_28_daily_certification%20ab8afc076a874daa89def694abc30668/ex5.jpeg)

<a> 태그의 경우 opening 태그 안에도 다른 내용이 들어가있어서 처리가 까다로울 수 있는데 어차피 태그 명은 앞부분에 존재하므로 closing 태그와 매칭을 확일할 때 indexOf를 이용해 확인했다.

**주의 사항으로는 opening 태그 없이 closing 태그가 나오는 경우이다.** 

이 경우는 예외 처리로 illegal을 출력하도록 해야 한다. 예외 처리 없이 제출할 경우 EmptyStackException이 발생한다. Exception 종류만으로 충분히 유추 가능해서 코드를 고치는 것은 어렵지 않다.

**하지만 실제 시험이라면 제출 결과를 알수 없으므로 통하지 않는 방법이다.**

5개 예제 모두 closing 태그가 opening 태그 이후에만 나오므로 예제만으로는 파악하기 어렵다.

### 코드

```kotlin
import java.util.*
import java.io.*

class BOJ_5076 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var input = ""

    fun solve() {
        while(true) {
            input = br.readLine()

            if(input == "#") {
                print(sb)
                break
            }

            val tagStack = Stack<String>()  //  열리는 태그가 끝날 때

            var closed = true
            val refinedInput = StringBuilder()  //  정제된 input

            //  input을 정제하기
            for(ch in input) {
                if(ch == '<')   //  태그 시작할 경우
                    closed = false
                else if(ch == '>') {    //  태그 끝날 경우
                    refinedInput.append(ch)
                    closed = true
                }
                else {  //  태그가 열린 상태에서는 다 저장
                    if(!closed)
                        refinedInput.append(ch)
                }
            }

            var legal = true

            tokens = StringTokenizer(refinedInput.toString(), ">")
            while(tokens.hasMoreTokens()) {
                val tag = tokens.nextToken()    //  태그

                if(tag[0] == '/') {  //  닫히는 태그면
                    val tagName = tag.substring(1)

                    if(tagStack.isEmpty()) { //  열리는 태그 없이 닫히는 태그가 나온 경우
                        legal = false
                        break
                    }
                    if(tagStack.peek().indexOf(tagName) == 0)   //  열리는 태그와 일치하면
                        tagStack.pop()  //  열리는 태그 스택에서 제거
                }
                else if(tag[tag.length - 1] != '/')  //  self tag가 아니면
                    tagStack.push(tag)  //  열리는 태그이므로 스택에 넣기
            }

            //  tagStack이 비어있으면
            sb.append(if(!legal) "illegal\n" else if(tagStack.isEmpty()) "legal\n" else "illegal\n")
        }
    }
}

fun main() {
    BOJ_5076().solve()
}
```