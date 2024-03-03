# 24_03_03_daily_certification

# React

## useEffect

App 컴포넌트가 다음과 같으면 버튼을 클릭할 때마다 setState에 의해 state가 변경되어 App 컴포넌트 전체가 리렌더링될 것이다.

```jsx
import { useState } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const onClick = () => setCounter((current) => current + 1);
  console.log("render");

  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
```

당연히 로그도 최초 렌더링 포함, 리렌더링될때마다 출력될 것이다.

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled.png)

최초 렌더링시에만 로그를 출력할 수 있을까?

**최초 렌더링시에만 특정 메서드가 동작할 수 있을까?**

좀 더 실용적인 예를 들어보자.

어떤 컴포넌트에서 API 호출을 통해 대량의 데이터를 가져온다고 가정하자. 최초 렌더링 시에 가져온 데이터를 state가 변경되었다고 리렌더링될 때마다 다시 호출해서 데이터를 가져올 필요는 없다. 아니, 가져와서는 안된다.

```jsx
import { useState } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const onClick = () => setCounter((current) => current + 1);
  
	//  API 호출
  console.log("CALL THE API...");

  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
```

### useEffect

useEffect를 import하고 useEffect의 첫 번째 인자로 최초 렌더링 시에만 실행될 함수를 넣어주고, 두 번째 인자로 []를 넣어주면, 해당 함수는 최초 렌더링 시에만 실행된다.

```jsx
import { useState, useEffect } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const onClick = () => setCounter((current) => current + 1);
  
  //  렌더링때마다 출력될 로그
  console.log("I run all the time");

  //  최초 렌더링 시에만 실행될 함수
  const iRunOnlyOnce = () => {
    console.log("I run only once.");
  }

  useEffect(iRunOnlyOnce, []);

  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
```

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%201.png)

최초 렌더링 포함 총 5번 렌더링되었지만 iRunOnlyOnce는 최초 1회만 실행되었다.

```jsx
import { useState, useEffect } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const onClick = () => setCounter((current) => current + 1);

	//  렌더링때마다 출력될 로그
  console.log("I run all the time");

  //  최초 렌더링 시 API 호출
  useEffect(() => {
    console.log("CALL THE API...");
  }, []);

  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
```

이번에는 입력창에 입력할 때마다 해당 내용을 로그로 보여주도록 하고 싶다고 가정해보자. 입력이 들어올 때마다 리렌더링을 해줘야 한다.

```jsx
import { useState, useEffect } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const [keyword, setKeyword] = useState("");

  const onClick = () => setCounter((current) => current + 1);
  const onChange = (event) => setKeyword(event.target.value);

  //  검색창에 입력할 때마다 출력
  console.log("search for", keyword);

  //  최초 렌더링 시 API 호출
  useEffect(() => {
    console.log("CALL THE API...");
  }, []);

  return (
    <div>
      <input
        value={keyword}
        onChange={onChange}
        type="text"
        placeholder="Search here..."
      />
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
```

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%202.png)

useState를  사용해서 입력할 때마다 keyword를 갱신해주도록 할 수 있다.

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%203.png)

하지만 counter와  같은 다른 state의 변경에도 해당 메서드가 동작할 수 있다. 버튼을 눌러서 counter를 증가시켰을 뿐인데도 로그가 출력되었다.

 

```jsx
//  검색창에 입력할 때마다 출력
console.log("search for", keyword);
```

위의 로그는 keyword가 갱신될 때에도 출력되지만, counter가 변경될 때에도 출력한다. 이는 우리가 원하는 동작이 아니다.

오로지 keyword의 변화가 발생할 시에만 동작하고 싶다.

```jsx
import { useState, useEffect } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const [keyword, setKeyword] = useState("");

  const onClick = () => setCounter((current) => current + 1);
  const onChange = (event) => setKeyword(event.target.value);
  
  //  최초 렌더링 시 API 호출
  useEffect(() => {
    console.log("CALL THE API...");
  }, []);

  //  검색창에 입력할 때마다 출력
  useEffect(() => {
    console.log("search for", keyword);
  }, [keyword]);

  return (
    <div>
      <input
        value={keyword}
        onChange={onChange}
        type="text"
        placeholder="Search here..."
      />
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;

```

**useEffect의 두 번째 인자의 배열 안에 변화를 감지할 state를 넣어준다.**

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%204.png)

검색창에 입력해서 keyword가 변할 때마다 로그가 출력된다.

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%205.png)

버튼을 눌러 counter를 증가시켰지만 추라적으로 로그가 출력되지 않는다. 오로지 keyword의 변화만 감지하여 로그를 출력한다.

useEffect의 두 번째 인자로 빈 배열을 넘겨주었을 경우, 변화를 감지할 state가 없어서 최초 렌더링 시에만 실행되는 것이다.

사소한 문제지만 useEffect에 keyword를 넘겨주어도 최초 렌더링 시에도 로글르 출력하는 것을 알 수 있다. 최초 렌더링 시에 작동하는 것은 useEffect가 정상 동작하는 것이지만, 기능 상 최초에는 검색을 하지 않도록 구현하려면, 구체적으로는 최초 렌더링 포함, 검색창이 비었을 때는 검색하지 않도록 구현하려면

```jsx
//  검색창에 입력할 때마다 출력
useEffect(() => {
  if (keyword !== "") {
    console.log("search for", keyword);
  }
}, [keyword]);
```

조건문을 걸어주면 된다. 더 구체적으로 길이가 5 이상일 때만 검색하길 원한다면

```jsx
//  검색창에 입력할 때마다 출력
useEffect(() => {
  if (keyword !== "" && keyword.length >= 5) {
    console.log("search for", keyword);
  }
}, [keyword]);
```

각각의 state마다 useEffect를 걸어두면 각각의 변경을 따로 감지해서 그 때마다 동작을 달리 할 수 있다.

```jsx
import { useState, useEffect } from "react";

function App() {
  const [counter, setCounter] = useState(0);
  const [keyword, setKeyword] = useState("");

  const onClick = () => setCounter((current) => current + 1);
  const onChange = (event) => setKeyword(event.target.value);

  //  최초 렌더링 시 로그
  useEffect(() => {
    console.log("I run only once.");
  }, []);

  // 버튼 누를 때마다 출력
  useEffect(() => {
    console.log("I run when 'counter' changes.");
  }, [counter]);

  //  검색창에 입력할 때마다 출력
  useEffect(() => {
    console.log("I run when 'keyword' changes.");
  }, [keyword]);

  return (
    <div>
      <input
        value={keyword}
        onChange={onChange}
        type="text"
        placeholder="Search here..."
      />
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
```

추가로 만약 keyword 또는 counter 둘 중 하나라도 변경되면 출력하는 로그는 

```jsx
//  버튼 누르거나, 검색창에 입력할 때마다 출력
  useEffect(() => {
    console.log("I run when 'counter' or 'keyword' changes.");
  }, [counter, keyword]);
```

useEffect에 감지할 state를 모두 전달하면 된다.

**useEffect가 변경을 감지할 state를 Dependency라고 한다.**

### Cleanup

```jsx
import { useState } from "react";

function App() {
  const [showing, setShowing] = useState(false);

  const onClick = () => setShowing((prev) => !prev);

  return (
    <div>
      <button onClick={onClick}>{showing ? "Hide" : "Show"}</button>
    </div>
  );
}

export default App;

```

버튼을 누를 때마다 Hide와 Show를 번갈아 보여준다.

이번에는 버튼을 누를 때마다 Hello라는 간단한 컴포넌트를 보여주거나 숨기는 기능을 만들어보자.

```jsx
const Hello = () => {
    return <h1>Hello</h1>;
}

export default Hello;
```

```jsx
import { useState } from "react";
import Hello from "./Hello";

function App() {
  const [showing, setShowing] = useState(false);

  const onClick = () => setShowing((prev) => !prev);

  return (
    <div>
      {showing ? <Hello /> : null}
      <button onClick={onClick}>{showing ? "Hide" : "Show"}</button>
    </div>
  );
}

export default App;

```

여기까지는 전혀 새로운 것이 없다. Hello 컴포넌트에 useEffect를 추가해서 최초 렌더링 시에 로그를 출력하도록 해보자.

```jsx
import { useEffect } from "react";

const Hello = () => {
  useEffect(() => console.log("I'm here!"), []);

  return <h1>Hello</h1>;
};

export default Hello;

```

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%206.png)

최초에는 Hello 컴포넌트가 렌더링되지 않았으므로 아무 로그도 없고

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%207.png)

버튼을 누르면 Hello 컴포넌트가 렌더링되면서 로그도 출력된다.

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%208.png)

만약 버튼을 여러 번 누르면 Hello 컴포넌트가 사라졌다가 다시 나타나면서 로그도 여러 번 출력된다. 즉 여러 번 최초 렌더링된다.

위의 구현은 Hello 컴포넌트를 매번 제거했다가 다시 생성하는 것이다.

Hello 컴포넌트가 제거될 때도 로그를 출력할 수 있을까?

**구체적으로 Hello 컴포넌트가 생성되어 최초 렌더링될 때에는 “created :)”를, 제거될 때는 “destroyed :(”를 출력할 수 있을까?**

**가능하다. 제거 될 때 실행할동작을 return하면 된다.**

```jsx
import { useEffect } from "react";

const Hello = () => {
  useEffect(() => {
    console.log("created :)");
    return () => console.log("destroyed :(");
  }, []);

  return <h1>Hello</h1>;
};

export default Hello;

```

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%209.png)

생성될 시 created :) 가,

![Untitled](24_03_03_daily_certification%207b5049d04e2e47ecb8ea8ec1f7fd07e2/Untitled%2010.png)

제거될 시 destroyed :( 가 출력된다.

최초 렌더링 시 동작할 메서드를 hiFn, 컴포넌트 소멸 시 동작할 메서드를 byeFn이라 하면 다음과 같이 작성하면 위의 코드와 동일하게 동작한다. 이 때 hiFn은 byeFn을 반환해야 한다.

```jsx
import { useEffect } from "react";

const Hello = () => {
  function byeFn() {
    console.log("destroyed :(");
  }

  function hiFn() {
    console.log("created :)");
    return byeFn;
  }

  useEffect(hiFn, []);

  return <h1>Hello</h1>;
};

export default Hello;

```

# Problem Solving (Algorithm & SQL)

**BOJ 1375 나이**

[1375번: 나이](https://www.acmicpc.net/problem/1375)

정점의 개수가 적다면 Floyd-Warshall 알고리즘으로 O(N^3)의 시간 복잡도의 전처리 후 모든 임의의 두 쌍에 대해 O(1)의 시간 복잡도로 나이 비교가 가능하겠지만, 안타깝게도 N이 최대 1,000,000이라 Floyd-Warshal 알고리즘은 사용할 수 없다. 결국 인접 리스트로 그래프를 만들고 탐색을 통해 구해야 한다.

쿼리 개수 Q가 크다면 한 번의 탐색 후 모든 쿼리를 처리할 수 있도록 해야겠지만, Q가 최대 20이므로 매번 임의의 두 정점 사이를 탐색하도록 하면 된다. 

**실패한 코드 (NumberFormatException)**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<Long, Int>()  //  이름 : Long, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private var find = false                                  //  dfs 완료 시 find = true
    private val depth = IntArray(MAX) { -1 }                  // -1이면 루트, 그 이후부터 0, 1, 2, ...
    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken().toLong()
            val name2 = tokens.nextToken().toLong()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            depth[v2] = if(depth[v2] < depth[v1] + 1) depth[v1] + 1 else depth[v2]
            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken().toLong()
            val name2 = tokens.nextToken().toLong()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            if(depth[v1] == depth[v2])  //  둘이 루트로부터 상대적인 깊이가 같으면 비교할 수 없음
                sb.append("gg ")
            else {  //  상대적인 깊이가 다를 경우, 깊이가 더 깊은 쪽에서 거슬러 올라가다 깊이가 더 얕은 쪽을 만나면 깊이가 얕은 쪽의 나이가 더 많음
                val pred = if(depth[v1] < depth[v2]) v1 else v2 //  더 나이 많을 수 있는 쪽
                val succ = if(depth[v1] < depth[v2]) v2 else v1 //  더 나이 어릴 수 있는 쪽

                find = false
                Arrays.fill(visited, false)

                sb.append("${if(dfs(succ, pred)) pred else "gg"} ")
            }
        }

        println(sb)
    }

    private fun dfs(cur : Int, target : Int) : Boolean {
        if(find)    //  이미 target에 도달했을 경우, 더 이상 탐색 안해도 되므로 종료
            return true

        if(cur == target) {   //  target에 도달했을 경우
            find = true
            return true
        }

        visited[cur] = true

        for(next in graph[cur]) {
            if(!visited[next] && dfs(next, target)) //  next부터 탐색했을 때 target에 도달했으면
                return true //  true 반환
        }

        return false    //  결국 찾지 못하면 false 반환
    }
}

fun main() {
    BOJ_1375().solve()
}
```

예제에서 이름이 수로 되어 있고 길이가 6byte라 해서 크기가 최대 6byte인 정수를 이름으로 받는 줄 알았다. 알고 보니 예제의 이름이 수로 된 문자열인 것이고 결국 String으로 받아야 한다. 또한 정점 번호는 Map을 통해 풀이 중 임의로 정한 것이므로 결국엔 원래 주어진 이름으로 출력해야 한다.

**틀린 코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<String, Int>()  //  이름 : String, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private var find = false                                  //  dfs 완료 시 find = true
    private val depth = IntArray(MAX) { -1 }                  // -1이면 루트, 그 이후부터 0, 1, 2, ...
    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            depth[v2] = if(depth[v2] < depth[v1] + 1) depth[v1] + 1 else depth[v2]
            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            if(depth[v1] == depth[v2])  //  둘이 루트로부터 상대적인 깊이가 같으면 비교할 수 없음
                sb.append("gg ")
            else {  //  상대적인 깊이가 다를 경우, 깊이가 더 깊은 쪽에서 거슬러 올라가다 깊이가 더 얕은 쪽을 만나면 깊이가 얕은 쪽의 나이가 더 많음
                val pred = if(depth[v1] < depth[v2]) v1 else v2 //  더 나이 많을 수 있는 쪽
                val succ = if(depth[v1] < depth[v2]) v2 else v1 //  더 나이 어릴 수 있는 쪽
                val predName = if(depth[v1] < depth[v2]) name1 else name2

                find = false
                Arrays.fill(visited, false)

                sb.append("${if(dfs(succ, pred)) predName else "gg"} ")
            }
        }

        println(sb)
    }

    private fun dfs(cur : Int, target : Int) : Boolean {
        if(find)    //  이미 target에 도달했을 경우, 더 이상 탐색 안해도 되므로 종료
            return true

        if(cur == target) {   //  target에 도달했을 경우
            find = true
            return true
        }

        visited[cur] = true

        for(next in graph[cur]) {
            if(!visited[next] && dfs(next, target)) //  next부터 탐색했을 때 target에 도달했으면
                return true //  true 반환
        }

        return false    //  결국 찾지 못하면 false 반환
    }
}

fun main() {
    BOJ_1375().solve()
}
```

처음 그래프를 만드는 과정에서 그래프의 각 정점의 상대적인 깊이를 정하고 매 쿼리에서 깊이가 깊은 쪽에서 깊이가 얕은 쪽으로 탐색을 했었다. 정확한 이유는 모르겠지만, 논리적인 비약이 존재하는 것 같다.

결국에는 a b에서 a에서 b로 도달 가능한지, b에서 a로 도달 가능한지 둘 다 확인해봐야 한다.

**또 틀린 코드(DFS)**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<String, Int>()  //  이름 : String, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private var find = false                                  //  dfs 완료 시 find = true
    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if (nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if (nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1 = nameMap.getOrDefault(name1, -1)
            val v2 = nameMap.getOrDefault(name2, -1)    //  비교 관계에서 나타난 적 없는 이름이면 -1

            if(v1 == -1 || v2 == -1)    //  둘 중 하나라도 알려진 게 없으면 비교 불가
                sb.append("gg ")
            else {  //  상대적인 깊이가 다를 경우, 깊이가 더 깊은 쪽에서 거슬러 올라가다 깊이가 더 얕은 쪽을 만나면 깊이가 얕은 쪽의 나이가 더 많음
                Arrays.fill(visited, false)
                find = false

                if (dfs(v1, v2)) {  //  v1부터 거슬러 올라가 v2에 도달할 수 있을 경우
//                    println("if")
                    sb.append("$name2 ") //  name2가 더 나이 많음
                }
                else {
                    Arrays.fill(visited, false)
                    find = false

//                    println("elif")
                    if (dfs(v2, v1))  //  v2부터 거슬러 올라가 v1에 도달할 수 있을 경우
                        sb.append("$name1 ") //  name1이 더 나이 많음
                    else
                        sb.append("gg ")
                }
            }
        }

        println(sb)
    }

    private fun dfs(cur: Int, target: Int): Boolean {
//        println("dfs($cur, $target)")

        if (find)    //  이미 target에 도달했을 경우, 더 이상 탐색 안해도 되므로 종료
            return true

        if (cur == target) {   //  target에 도달했을 경우
            find = true
            return true
        }

        visited[cur] = true

        for (next in graph[cur]) {
            if (!visited[next] && dfs(next, target)) //  next부터 탐색했을 때 target에 도달했으면
                return true //  true 반환
        }

        return false    //  결국 찾지 못하면 false 반환
    }
}

fun main() {
    BOJ_1375().solve()
}
```

DFS로 a에서 b로 도달 가능한지 확인하는 코드를 작성했는데 결국 또 틀렸다. 몇 번을 고쳐봐도 59%의 벽을 넘지 못하고 있는데 무슨 일일까.

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<String, Int>()  //  이름 : String, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if (nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if (nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1 = nameMap.getOrDefault(name1, -1)
            val v2 = nameMap.getOrDefault(name2, -1)    //  비교 관계에서 나타난 적 없는 이름이면 -1

            if(v1 == -1 || v2 == -1)    //  둘 중 하나라도 알려진 게 없으면 비교 불가
                sb.append("gg ")
            else {  //  한 쪽에서부터 시작해서 다른 쪽에 도달 가능하면, 다른 쪽이 더 나이가 많은 것임
                if(bfs(v1, v2))     //  v1에서부터 시작해서 v2에 도달 가능하면
                    sb.append("$name2 ")    //  name2가 name1보다 나이가 많음
                else if(bfs(v2, v1))    //  v2에서부터 시작해서 v1에 도달 가능하면
                    sb.append("$name1 ")    //  name1이 name2보다 나이가 많음
                else
                    sb.append("gg ")
            }
        }

        println(sb)
    }

    private fun bfs(from : Int, to : Int) : Boolean {
        val q = LinkedList<Int>()
        Arrays.fill(visited, false)

        visited[from] = true
        q.offer(from)

        while(q.isNotEmpty()) {
            val cur = q.poll()  //  현재 정점

            for(next in graph[cur]) {   //  주변 다음 정점 탐색
                if(visited[next])   //  이미 방문한 적 있으면 skip
                    continue

                if(next == to)  //  다음 정점이 목적 정점일 경우
                    return true //  찾음

                visited[next] = true    //  다음 정점부터 탐색
                q.offer(next)
            }
        }   //  while-end

        return false    //  찾지 못함
    }   //  bfs-end
}

fun main() {
    BOJ_1375().solve()
}
```

결국 BFS로 풀어서 맞았다.

**생각해볼 문제**

1. DFS로 했을 때 왜 틀리는 건지, DFS 코드 어떤 게 잘못됐는지
2. 상대적으로 깊이를 파악해서 비교하는게 어떤 점에서 틀렸는지