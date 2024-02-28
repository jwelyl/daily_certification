# 24_02_28_daily_certification

# Typescript

## Union Type

```tsx
type A = B | C | D | F;
```

A는 타입 B, C, D, F 중 하나의 타입을 가질 수 있는 Union Type이다. Union Type을 사용하면 타입 유연성을 높일 수 있다.

```tsx
type StringOrNumber = string | number;

function logMessage(message: StringOrNumber) {
  console.log(message);
}

logMessage("hello");  // 문자열 사용 가능
logMessage(123);      // 숫자 사용 가능
// logMessage(true);  // 오류: boolean 타입은 StringOrNumber 타입에 할당할 수 없음

```

logMessage 함수는 Union Type인 StringOrNumber 변수를 매개변수로 받으므로 string 또는 number 타입의 인자를 받을 수 있다.

## enum

Typescript에서 enum은 기본적으로 Numeric Enums이다. 명시적으로 값을 지정하지 않으면 다음 enum 타입은 A = 0부터 시작해서 1씩 증가된 값을 가진다.

```tsx
enum Enum {
  A, B, C, D
};
```

```tsx
enum Enum {
    A,
    B,
    C,
    D
};

console.log(Enum.A) //  0
console.log(Enum.B) //  1
console.log(Enum.C) //  2
console.log(Enum.D) //  3
```

![Untitled](24_02_28_daily_certification%20ab8afc076a874daa89def694abc30668/Untitled.png)

```tsx
enum Enum {
    A,
    B,
    E,
    C,
    D
};

console.log(Enum.A) //  0
console.log(Enum.B) //  1
console.log(Enum.C) //  2
console.log(Enum.D) //  3
```

만약 명시적인 값 지정 없이 B와 C 사이에 새로운 멤버 E를 추가할 경우, 새로 추가된 E 멤버가 2 값을 가지게 되고 이후의 C, D 값은 값이 1씩 증가하게 된다.

![Untitled](24_02_28_daily_certification%20ab8afc076a874daa89def694abc30668/Untitled%201.png)

중간에 새로운 멤버를 추가할 경우, 기존에 C, D 멤버를 사용하던 코드에서 문제가 발생하게 된다.

이를 해결하기 위해 String Enums를 사용한다. Typescript에서 enum 멤버가 string 값을 가지려면 명시적으로 값을 지정해줘야 한다.

```tsx
enum Enum {
    A = "A",
    B = "B",
    C = "C",
    D = "D"
};

console.log(Enum.A) //  A
console.log(Enum.B) //  B
console.log(Enum.C) //  C
console.log(Enum.D) //  D
```

![Untitled](24_02_28_daily_certification%20ab8afc076a874daa89def694abc30668/Untitled%202.png)

B, C 사이에 E를 추가해줘도 E 이후의 값이 변하지 않으므로 기존에 C, D를 사용하는 코드에서 문제를 일으키지 않는다.

```tsx
enum Enum {
    A = "A",
    B = "B",
    E = "E",
    C = "C",
    D = "D"
};

console.log(Enum.A) //  A
console.log(Enum.B) //  B
console.log(Enum.C) //  C
console.log(Enum.D) //  D
```

![Untitled](24_02_28_daily_certification%20ab8afc076a874daa89def694abc30668/Untitled%202.png)

Numeric Enums을 사용할 때에도 명시적으로 값을 지정해준다면 같은 효과를 기대할 수 있다. 가독성 및 유지보수성이 좋고, 실제 값과 enum 멤버명이 같아 디버깅 시에도 용이하고 충돌을 방지하기도 쉽다.

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

            var legal = true    //  중간에 오류가 발생할 경우 false

            tokens = StringTokenizer(refinedInput.toString(), ">")
            while(tokens.hasMoreTokens()) {
                val tag = tokens.nextToken()    //  태그

                if(tag[0] == '/') {  //  닫히는 태그면
                    val tagName = tag.substring(1)

                    if (tagStack.isEmpty()) { //  열리는 태그 없이 닫히는 태그가 나온 경우
                        legal = false
                        break
                    }
                    if (tagStack.peek().indexOf(tagName) != 0) { //  열리는 태그와 일치하지 않으면
                        legal = false
                        break
                    }
                    else tagStack.pop() //  닫히는 태그와 매칭되는 열리는 태그 제거
                }
                else if(tag[tag.length - 1] != '/')  //  self tag가 아니면
                    tagStack.push(tag)  //  열리는 태그이므로 스택에 넣기
            }

            //  중간에 오류가 없고, tagStack이 비어있으면 legal, 그렇지 않으면 illegal
            //  opening tag가 남으면 중간에 오류가 없지만 tagStack이 비어있지 않게 됨
            sb.append(if(legal && tagStack.isEmpty()) "legal\n" else "illegal\n")
        }
    }
}

fun main() {
    BOJ_5076().solve()
}
```