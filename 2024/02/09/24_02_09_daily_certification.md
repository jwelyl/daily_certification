# 24_02_09_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 6581 HTML**

[6581번: HTML](https://www.acmicpc.net/problem/6581)

입력 HTML을 한 줄씩 읽고, tokenizer로 띄어쓰기 단위로 단어를 파싱한다.

단어들을 처리하기 위한 buffer를 따로 준비하고 buffer 크기로 단어를 추가할지 관리한다.

1. 단어가 <br> 태그일 경우
    
    buffer 내용을 출력하고 buffer를 비운 후 줄바꿈을 한다.
    
2. 단어가 <hr> 태그일 경우
    
    HTML 문장 내에서 이전에 처리한 단어가 존재할 경우 buffer 내용을 출력 후 buffer를 비우고 줄바꿈을 한다. 그 다음에 수평선을 출력하고 줄바꿈을 한다.
    
3. 일반 단어일 경우
    1. 현재 buffer가 비었을 경우 그냥 추가하면 된다.
    2. 현재 buffer가 비어있지 않을 경우
        1. buffer 크기 + 띄어쓰기 + 단어 크기가 80을 넘을 경우 buffer를 출력하고 줄바꿈후 buffer를 비운 다음에 현재 단어를 buffer에 다시 추가한다.
        2. buffer 크기 + 띄어쓰기 + 단어 크기가 80 이하일 경우 띄어쓰기 추가 후 단어를 추가한다.

어렵진 않지만 문제 조건을 꼼꼼히 분석하고 구현해야 하는 좋은 문자열 파싱 연습 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_6581 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val buffer = StringBuilder()

    fun solve() {
        while(true) {
            val input = br.readLine()   //  한 줄 단위로 입력받기

            if(input == null) { //  끝났을 경우
                if(buffer.isNotEmpty()) {   //  buffer가 비어있지 않을 경우
                    sb.append(buffer)       //  buffer 옮기기
                    buffer.clear()          //  buffer 비우기
                    sb.append("\n")         //  줄 바꾸기
                }

                print(sb)   //  파싱 결과 출력
                break
            }

            tokens = StringTokenizer(input) //  단어 단위로 파싱

            var before = "" //  해당 line에서 이전에 처리한 단어
            while(tokens.hasMoreTokens()) {
                val word = tokens.nextToken()

                if(word == "<br>") {    //  새 줄 시작하기
                    sb.append(buffer)   //  buffer에 존재하는 내용 옮기기
                    buffer.clear()      //  buffer 비우기
                    sb.append("\n")     //  줄 바꾸기
                }
                else if(word == "<hr>") {   //  수평선 긋기
                    if(before != "") {  //  이전에 처리한 단어가 있을 경우, 즉 <hr>이 가장 먼저 나온게 아닐 경우
                        if(buffer.isNotEmpty()) {   //  buffer에 내용 남았을 경우
                            sb.append(buffer)   //  buffer에 존재하는 내용 옮기기
                            buffer.clear()      //  buffer 비우기
                            sb.append("\n")     //  줄 바꾸기
                        }

                        repeat(80) {
                            sb.append("-")
                        }
                        sb.append("\n")
                    }
                }
                else {  //  일반 단어일 경우
                    if(buffer.isEmpty())    //  buffer가 비었을 경우
                        buffer.append(word) //  띄어쓰기 없이 단어 추가
                    else {
                        val curSize = buffer.length //  현재 buffer 크기

                        if(curSize + 1 + word.length > 80) { //  현재 buffer + 띄어쓰기 + 현재 단어 길이 > 80일 경우
                            sb.append(buffer)   //  buffer에 있는 내용 옮기기
                            buffer.clear()      //  buffer 비우기
                            sb.append("\n")     //  줄 바꾸기
                            buffer.append(word) //  새로운 buffer에 단어 추가하기
                        }
                        else // 띄어쓰고 현재 단어 추가해도 길이가 80을 넘지 않을 경우
                            buffer.append(" $word")
                    }
                }

                before = word
            }
        }
    }
}

fun main() {
    BOJ_6581().solve()
}
```