# 24_03_02_daily_certification

# React

## Javascript Object Destructuring

```jsx
const person = {
  name: 'John Doe',
  age: 30
};

const { name, age } = person;
console.log(name, age); // John Doe, 30
```

## props

디음과 같은 버튼 컴포넌트 Btn이 있을 때 의미 상 props의 text로는 string을, fontSize로는 number를  받고 싶어함이 명확하다.

```jsx
const Btn = ({text, fontSize}) => {
  console.log(text, "renderd");

  return (
    <button
      style={{
        backgroundColor: "tomato",
        color: "white",
        padding: "10px 20px",
        border: 0,
        borderRadius: "10px",
        fontSize,
      }}
    >
      {text}
    </button>
  );
};
```

하지만 Btn을 사용하는 부모 컴포넌트에서 text에 number를, fontSize로 string을 준다 해도 코드 상으로는 문제가 없다. 하지만 논리적으론 오류가 된다. 이는 매우 곤란한 일이다.

```jsx
const App = () => {
  return (
    <div>
      <Btn text={"Save Changes"} fontSize={14}/>
      <Btn text={14} fontSize={"Hello"} />
    </div>
  );
};
```

props에서 타입 검사를 해준다면 정말 좋을텐데, 아쉽게도 ReactJS에는 그런 기능이 없다.

PropTypes를 추가하면 가능하다.

[https://unpkg.com/react@17.0.2/umd/react.production.min.js](https://unpkg.com/react@17.0.2/umd/react.production.min.js) 를 [https://unpkg.com/react@17.0.2/umd/react.development.js](https://unpkg.com/react@17.0.2/umd/react.development.js) 로 수정하고 다음을 추가한다.

```jsx
<script src="https://unpkg.com/prop-types@15.7.2/prop-types.js"></script>
```

Btn 컴포넌트를 정의한 후에 다음 코드를 추가하여 Btn 컴포넌트의 props 타입을 정의한다.

```jsx
Btn.propTypes = {
  text: PropTypes.string,
  fontSize: PropTypes.number
}
```

그러면 개발자 도구에서 다음과 같은 warning을 통해 props 타입이 잘못 전달되었음을 알려준다.

![Untitled](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/Untitled.png)

타입뿐만 아니라 필수 여부도 정할 수 있다. text와 fontSize를 모두 필수로 지정하고

```jsx
Btn.propTypes = {
  text: PropTypes.string.isRequired,
  fontSize: PropTypes.number.isRequired
}
```

필수로 지정된 fontSize를 전달해주지 않으면

```jsx
const App = () => {
  return (
    <div>
      <Btn text={"Save Changes"} fontSize={14}/>
      <Btn text={"Hello"} />
    </div>
  );
};
```

해당 경고를 통해 알려준다.

![Untitled](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/Untitled%201.png)

JS 문법을 이용해 컴포넌트를 정의할 때 props의 기본값도 정의할 수 있다.

```jsx
const Btn = ({text, fontSize = 14}) => {
  return (
    <button
      style={{
        backgroundColor: "tomato",
        color: "white",
        padding: "10px 20px",
        border: 0,
        borderRadius: "10px",
        fontSize,
      }}
    >
      {text}
    </button>
  );
};

```

기본값이 설정되어 있어도 PropTypes에서 isRequired로 설정했으면 전달하지 않았을 시에 경고는 발생한다.

## create-react-app

```bash
npx create-react-app myapp
cd myapp
npm start
```

## 컴포넌트 분리

컴포넌트 별로 다른 JS 파일로 작성하여 관리하는 것이 좋다. 이렇게 작성한 컴포넌트를 다른 파일의 컴포넌트에서 사용하기 위해 export 키워드를 사용한다.

```jsx
const Button = ({text}) => {
    return <button>{text}</button>
}

export default Button;
```

다른 컴포넌트에서 Button 컴포넌트를 import하면 사용할 수 있다.

```jsx
import Button from "./Button";

function App() {
  return (
    <div>
      <h1>Welcome Back!</h1>
      <Button text={"continue"}/>
    </div>
  );
}

export default App;

```

create-react-app으로 생성한 React 프로젝트에서도 PropTypes를 사용할 수 있다. 최상위 디렉토리로 이동한 후 다음 명령어를 입력해 prop-types를 설치한다.

```bash
npm i prop-types
```

Button 컴포넌트에 PropTypes를 추가할 수 있다. 설치한 prop-types에서 PropTypes를 import한 후 사용할 수 있다.

```jsx
import PropTypes from "prop-types";

const Button = ({text}) => {
    return <button>{text}</button>
}

Button.propTypes = {
    text: PropTypes.string.isRequired
}

export default Button;
```

다음과 같이 styles.css 파일을 만들고 import하면

```css
button {
    color: white;
    background-color: tomato;
}
```

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import "./styles.css";

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

모든 button 태그에 css가 적용된다.

개별적으로 서로 다른 css를 적용하고 싶으면 이런 방법은 안된다. 그렇다고 일일이 컴포넌트마다 태그에 css를 작성하고 싶지도 않고, props로 일일이 주고 싶지도 않다.

Button 컴포넌트에만 css를 적용하고 싶으면 Button.module.css를 작성해야 한다. 다른 이름도 상관없지만 보통 적용시킬 컴포넌트와 같은 이름으로 한다. 확장자는 반드시 module.css여야 한다.

**Button.module.css**

```css
.btn {
    color: white;
    background-color: tomato;
}
```

**Button.js**

```jsx
import PropTypes from "prop-types";
import styles from "./Button.module.css"

const Button = ({text}) => {
    return <button className={styles.btn}>{text}</button>
}

Button.propTypes = {
    text: PropTypes.string.isRequired
}

export default Button;
```

**App.module.css**

```css
.title {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
```

**App.js**

```jsx
import Button from "./Button";
import styles from "./App.module.css";

function App() {
  return (
    <div>
      <h1 className={styles.title}>Welcome Back!</h1>
      <Button text={"Continue"}/>
    </div>
  );
}

export default App;
```

# Problem Solving (Algorithm & SQL)

**BOJ 20171 Dessert Café**

[20171번: Dessert Café](https://www.acmicpc.net/problem/20171)

문제가 영어로 되어 있어 이해가 잘 안 됐지만 예제 1과 2를 보면 결국 아파트 단지를 포함하여, 임의의 두 아파트 단지 사이의 정점 개수를 구하는 문제이다.

[ex.avif](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex.avif)

일단 임의의 아파트 단지 정점에서부터 시작해서 dfs로 탐색한다. 주변 정점이 이미 탐색한 정점이면(checke가 0이 아니면) 탐색을 스킵하고 아직 탐색하지 않은 정점이면 탐색한다. 현재 정점이 아파트 단지거나, 주변 정점이 아파트 단지로 이어지면(주변 정점 중 checked가 1인 경우가 존재하면) checked를 1로 해서 반환하고, 현재 정점이 아파트 단지도 아니고, 주변 정점 중 아파트 단지로 이어지는 정점도 없으면 checked를 -1로 해서 반환한다.

주변 정점에 대한 dfs가 모두 끝난 후에야 현재 정점으로 돌아오므로 별 다른 처리가 없으면 두 정점을 반복해서 dfs하게 되므로 일단 dfs로 한 정점을 탐색 시작하면 checked를 2로 설정하여 다시 dfs하는 일이 없도록 하였다.

**예제 1**

![ex1_1.jpeg](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex1_1.jpeg)

아파트 단지인 정점 2에서 시작하여 다른 아파트 단지까지 가는 중간에 거치게 되는 정점들을 모두 포함하면 된다.

![ex1_2.jpeg](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex1_2.jpeg)

정점 1, 3, 7의 경우 아파트 단지인 2에서부터 시작해서 갔을 때 다른 아파트 단지와 이어지지도 않고, 그 자신도 아파트 단지가 아니므로 not ok가 된다. 그 외 정점들은 그 자신이 아파트 단지이거나 정점 2와 아파트 단지 사이의 경로에 존재하므로 ok이다.

![ex3jpeg.jpeg](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex3jpeg.jpeg)

다른 아파트 단지를 만났다고 탐색을 종료해서는 안된다. 위와 같은 그래프의 경우 2에서 시작해서 또 다른 아파트 단지 4를 만나고, 4에서부터 또 다른 아파트 단지를 찾아 그 사이에 있는 정점도 추가해줘야 한다.

### 코드

```kotlin
import java.util.*
import java.io.*

class BOJ_20171 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    정점 개수
    private var k = 0    //    아파트 단지 개수

    private lateinit var graph : Array<ArrayList<Int>>    //    그래프
    private lateinit var isComplex : BooleanArray         //    아파트 단지면 true
    private lateinit var checked : IntArray    // 0이면 아직 미방문, 1이면 ok, -1이면 not ok, 2면 대기 중

    private var start = -1     //    dfs 시작할 아파트 단지
    private var ans = 0        //    ok인 지점 개수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        graph = Array(n + 1) { ArrayList() }
        isComplex = BooleanArray(n + 1)
        checked = IntArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()    //    두 정점 사이 간선
            tokens.nextToken()    //    가중치 필요 없음

            graph[v1].add(v2)
            graph[v2].add(v1)
        }

        tokens = StringTokenizer(br.readLine())
        repeat(k) {
            val v = tokens.nextToken().toInt()
            isComplex[v] = true

            if(start == -1)
                start = v    //  dfs 시작할 정점 정하기
        }

        dfs(start)

        println(ans)
    }

    private fun dfs(v : Int) : Int {
        checked[v] = 2  //  일단 방문했고, 다시 방문할 일 없게 대기 중으로 바꾸기

        var isOk = false    //  현재 v 정점이 다른 아파트 단지까지 경로에 포함될 경우

        for(nv in graph[v]) {   //  v의 주변 정점 nv 탐색
            if(checked[nv] != 0)    //  이미 체크했으면 skip
                continue

            if(dfs(nv) == 1)    //  주변 정점 nv가 아파트 단지까지의 경로에 포함될 경우
                isOk = true     //  v도 포함됨
        }

        if(isComplex[v] || isOk) {  //  v가 아파트 단지거나, v가 아파트 단지까지 경로에 포함될 경우
            ans++
            checked[v] = 1
        }
        else checked[v] = -1    //  포함되지 않을 경우

        return checked[v]
    }
}

fun main() {
    BOJ_20171().solve()
}

```