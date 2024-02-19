# 24_02_19_daily_certification

# Typescript

## Interface

### type 복습

type 키워드로 number, string 같은 타입에 alias를 설정하여 다른 이름(별칭)으로 접근할 수도 있고, object의 명세를 작성할 수도 있다.

```tsx
type Nickname = string;
type Health = number;
type Team = "red" | "blue" | "yellow";

type Player = {
  nickname: Nickname;
  healthBar: Health;
  team: Team;
};

const nico: Player = {
  nickname: "nico",
  healthBar: 10,
  team: "red",
};

type Food = string;

const kimch: Food = "delicious";
```

object의 명세를 작성하는 역할을 interface가 대신할 수 있다. 대신 타입에 alias를 설정하는 것은 불가하다.

참고로 Java interface와 다르게 property를 설정하는 것도 가능하다. (Java는 상수만 가능) 대신 property들도 public이여야 하며 상속받는 class에서도 public이다.

```tsx
type Nickname = string; // interface Nickname = string은 불가
type Health = number;
type Team = "red" | "blue" | "yellow";

interface Player {
  nickname: Nickname;
  healthBar: Health;
  team: Team;
}

const nico: Player = {
  nickname: "nico",
  healthBar: 10,
  team: "red",
};

type Food = string;

const kimch: Food = "delicious";
```

**하지만 interface가 보다 객체지향적이다.**

```tsx
interface User {
  name: string;
}

interface Player extends User {}

const nico: Player = {
  name: "nico",
};
```

type으로도 비슷하게 구현할 수는 있다.

```tsx
type User = {
  name: string;
};

type Player = User & {};

const nico: Player = {
  name: "nico",
};
```

&는 and를 의미하며, Player는 User와 추가적으로 서술하는 필드를 가지게 된다.

같은 interface 이름을 재정의하면 필드가 축적된다. 즉 아래와 같이 User를 세 번 정의하면

```tsx
interface User {
  name: string;
}

interface User {
  lastName: string;
}

interface User {
  health: number;
}

const nico: User = {
  name: "nico",
  lastName: "n",
  health: 10,
};
```

다음과 같은 interface가 된다. type으로는 할 수 없다.

```tsx
interface User {
  name: string;
  lastName: string;
  health: number;
}

const nico: User = {
  name: "nico",
  lastName: "n",
  health: 10,
};
```

## Abstract Class vs Javascript

### abstract class

```tsx
abstract class User {
  constructor(protected firstName: string, protected lastName: string) {}

  abstract fullName(): string;
  abstract sayHi(name: string): string;
}

class Player extends User {
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  sayHi(name: string) {
    return `Hello ${name}. My name is ${this.fullName()}`;
  }
}

// const user : User = new User("jaeseong", "heo") // 추상 클래스의 객체 만들 수 없음
const user: User = new Player("jaeseong", "heo");
```

위 TS 코드는 다음과 같은 JS 코드로 변환된다.

```jsx
"use strict";
class User {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}
class Player extends User {
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  sayHi(name) {
    return `Hello ${name}. My name is ${this.fullName()}`;
  }
}
// const user : User = new User("jaeseong", "heo")
const user = new Player("jaeseong", "heo");
```

### interface

```tsx
interface User {
  firstName: string;
  lastName: string;
  fullName(): string;
  sayHi(name: string): string;
}

class Player implements User {
  constructor(public firstName: string, public lastName: string) {}

  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  sayHi(name: string) {
    return `Hello ${name}. My name is ${this.fullName()}`;
  }
}
```

위 TS 코드는 다음과 같이 JS 코드로 변한다.

```jsx
"use strict";
class Player {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  sayHi(name) {
    return `Hello ${name}. My name is ${this.fullName()}`;
  }
}
```

User에 관한 내용이 존재하지 않아, 파일 크기가 줄어든다.

하나의 class가 여러 interface를 구현하는 것도 가능하다. Player는 User와 Human을 구현한다.

```jsx
interface User {
  firstName : string,
  lastName : string,
  fullName() : string,
  sayHi(name : string) : string
}

interface Human {
  health : number
}

class Player implements User, Human {
  constructor(
    public firstName : string,
    public lastName : string,
    public health : number
  ) {}

  fullName() {
    return `${this.firstName} ${this.lastName}`
  }

  sayHi(name : string) {
    return `Hello ${name}. My name is ${this.fullName()}`
  }
}
```

## Polymorphism

```tsx
interface MyStorage<T> {
  [key: string]: T;
}

class LocalStorage<T> {
  private storage: MyStorage<T> = {};

  set(key: string, value: T) {
    this.storage[key] = value;
  }

  remove(key: string) {
    delete this.storage[key];
  }

  get(key: string): T {
    return this.storage[key];
  }
  clear() {
    this.storage = {};
  }
}

const stringStorage = new LocalStorage<string>();
stringStorage.set("key", "value");

const strValue: string = stringStorage.get("key");

const booleanStorage = new LocalStorage<boolean>();
booleanStorage.set("key", false);

const boolValue: boolean = booleanStorage.get("key");
```

# Problem Solving (Algorithm & SQL)

**BOJ 17218 비밀번호 만들기**

[17218번: 비밀번호 만들기](https://www.acmicpc.net/problem/17218)

LCS를 실제로 출력하는 문제이다.

문제에서 요구하는 LCS가 Longest Common Subsequence인지, Longest Common Substring인지 잘 확인하고 문제를 풀어야 한다.

**Longest Commnon Subsequence**

가장 긴 공통 부분 수열을 구하는 것이다. 부분 수열인만큼 꼭 연속해서 고를 필요가 없다. 이번 문제에서 요구하는 것이 이 LCS이다.

이 문제의 예시처럼

AUTABBEHNSA와 BCUAMEFKAJNA의 Longest Common Subsequence는

A**U**T**A**BB**E**H**N**S**A** 와 BC**UA**M**E**FKAJ**NA** 이므로 **UAENA**이다. 연속해서 선택하지 않았음을 알 수 있다.

[9251번: LCS](https://www.acmicpc.net/problem/9251)

**Longest Commnon Substring**

가장 긴 공통 부분 문자열을 구하는 것이다. 부분 문자열인만큼 반드시 연속해서 골라야 한다. 위의 예시에서 길이 2 이상의 공통 부분 문자열은 없다.

[5582번: 공통 부분 문자열](https://www.acmicpc.net/problem/5582)

가장 긴 공통 부분 수열을 구하는 것으로 돌아와서 길이를 구하는 것은 9251번처럼 O(len1 _ len2)의 시간 복잡도로 한 번에 쉽게 구할 수 있다. 하지만 실제 LCS를 출력하기 위해서는 길이만 저장할 뿐만 아니라 추가적으로 O(len1 _ len2)의 공간복잡도를 사용해서 현재 (i, j)까지 최장 공통 부분 수열 이전에 어디서 LCS가 생성되는지를 저장하고 LCS가 완성된 다음에 역추적 과정을 거쳐야 한다.

![32123231.jpeg](24_02_19_daily_certification%2018fd3a4ed0174877ba013d65915e1fa8/32123231.jpeg)

**코드**

```kotlin
import java.util.*
import java.io.*

const val UL = 1
const val L  = 2
const val U  = 3

class BOJ_17218 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private lateinit var str1 : CharArray
    private lateinit var str2 : CharArray

    private lateinit var dp : Array<IntArray>
    private lateinit var dir : Array<IntArray>  //  dir[i][j] : (i, j)의 이전 칸이 어디였는지 저장

    private val dy = intArrayOf(0, -1, 0, -1)
    private val dx = intArrayOf(0, -1, -1, 0)

    private val stack = Stack<Char>()

    fun solve() {
        str1 = br.readLine().toCharArray()
        str2 = br.readLine().toCharArray()

        dp = Array(str1.size + 1) { IntArray(str2.size + 1) }
        dir = Array(str1.size + 1) { IntArray(str2.size + 1) }

        for(i in 1 .. str1.size) {
            for(j in 1 .. str2.size) {
                if(str1[i - 1] == str2[j - 1]) {    //  str1[i-1]과 str2[j-1]이 같을 경우
                    dp[i][j] = dp[i - 1][j - 1] + 1 //  str1[i-2]까지와 str2[j-2]까지의 최장 공통 길이 + 1이 str[i-1]까지와 str2[j-1]까지의 최장 공통 길이임
                    dir[i][j] = UL  //  str1[i-1]와 str2[i-1]은 str1[i-2]와 str2[i-2]의 LCS에서 파생됨
                }
                else {
                    if(dp[i][j - 1] >= dp[i - 1][j]) {  //  str1[i-1]까지와 str2[j-2]까지의 LCS가 가장 길 경우
                        dp[i][j] = dp[i][j - 1]         //  str1[i-1]까지와 str2[j-2]까지의 최장 공통 길이 + 1이 str1[i-1]까지와 str2[j-1]까지의 최장 공통 길이
                        dir[i][j] = L   //  str1[i-1]와 str2[i-1]은 str1[i-1]와 str2[i-2]의 LCS에서 파생됨
                    }
                    else {                             //  str1[i-2]까지와 str2[j-1]까지의 LCS가 가장 길 경우
                        dp[i][j] = dp[i - 1][j]        //  str1[i-2]까지와 str2[j-1]까지의 최장 공통 길이 + 1이 str1[i-1]까지와 str2[j-1]까지의 최장 공통 길이
                        dir[i][j] = U   //  str1[i-1]와 str2[i-1]은 str1[i-2]와 str2[i-1]의 LCS에서 파생됨
                    }
                }
            }
        }

        //  가장 끝에서부터 역추적
        var cy = str1.size
        var cx = str2.size

        //  cy = 0이거나, cx = 0이면 dir[cy][cx] = 0임
        while(dir[cy][cx] != 0) {   //  끝에 도달하기 전까지
            val dir = dir[cy][cx]

            val ny = cy + dy[dir]
            val nx = cx + dx[dir]

            if(dir == UL)   //  LCS 길이가 1 증가할 때마다
                stack.add(str1[cy - 1]) //  추가된 공통 부분 문자열의 문자 스택에 추가

            cy = ny
            cx = nx
        }

        while(stack.isNotEmpty())   //  스택 내용 출력
            sb.append(stack.pop())

        print(sb)
    }
}

fun main() {
    BOJ_17218().solve()
}
```
