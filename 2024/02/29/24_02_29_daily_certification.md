# 24_02_29_daily_certification

# React

## useState

```jsx
const [counter, setCounter] = React.useState(0);

setCounter(counter + 1);
```

setState로 state 변경 시 state가 변경됨과 동시에 state가 사용되는 컴포넌트가 자동으로 리렌더링된다.

리렌더링될 때는 페이지 전체가 아니라 바뀐 부분만 업데이트된다.

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled.png)

```jsx
const [counter, setCounter] = React.useState(0);

setCounter(counter + 1);
```

state(위에선 counter)를 위와 같은 방법으로 변경하는 것은 좋은 방법이 아니다. counter 값이 다른 곳에서도 변경되었을 수 있기 때문에 의도한 대로 현재 값 + 1이라는 결과가 나오지 않을 수도 있다. (비동기적으로 변할 수 있음)

**state의 현재 값을 토대로 새로운 값으로 갱신하고 싶으면 다음과 같이 하는 게 안전하다.**

```jsx
setCounter((current) => current + 1);
```

current가 현재의 counter 값임을 보장하기 때문에 새로운 counter도 의도한 대로 갱신된다.

### 단위 변환기 코드

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const MinutesToHours = () => {
      const [amount, setAmount] = React.useState(0);
      const [inverted, setInverted] = React.useState(false);

      const onChange = (event) => {
        setAmount(event.target.value);
      };

      const reset = () => setAmount(0);
      const onFlip = () => {
        reset();
        setInverted((current) => !current);
      };

      return (
        <div>
          <div>
            <label htmlFor="minutes">Minutes</label>
            <input
              id="minutes"
              value={inverted ? amount * 60 : amount}
              placeholder="Minutes"
              type="number"
              disabled={inverted}
              onChange={onChange}
            />
          </div>
          <div>
            <label htmlFor="hours">Hours</label>
            <input
              id="hours"
              value={inverted ? amount : amount / 60}
              placeholder="Hours"
              type="number"
              disabled={!inverted}
              onChange={onChange}
            />
          </div>
          <button onClick={reset}>Reset</button>
          <button onClick={onFlip}>Flip</button>
        </div>
      );
    }

    const KmsToMiles = () => {
      const [amount, setAmount] = React.useState(0);
      const [inverted, setInverted] = React.useState(false);

      const onChange = (event) => {
        setAmount(event.target.value);
      }

      const reset = () => {
        setAmount(0);
      }

      const onFlip = () => {
        reset();
        setInverted((current) => !current);
      }

      return (
        <div>
          <div>
            <label htmlFor="kms">KMs</label>
            <input
              id="kms"
              value={inverted ? amount * 1.60934 : amount}
              placeholder="kms"
              type="number"
              disabled={inverted}
              onChange={onChange}
            />
          </div>
          <div>
            <label htmlFor="miles">Miles</ label>
            <input
              id="miles"
              value={inverted ? amount : amount / 1.60934}
              placeholder="kms"
              type="number"
              disabled={!inverted}
              onChange={onChange}
            />
          </div>
          <button onClick={reset}>Reset</button>
          <button onClick={onFlip}>Flip</button>
        </div>
      );
    }

    const App = () => {
      const [index, setIndex] = React.useState("xx");

      const onSelect = (event) => {
        console.log(event.target.value);
        setIndex(event.target.value);
      };

      return (
        <div>
          <h1 className="hi">Super Converter</h1>
          <select value={index} onChange={onSelect}>
            <option value="xx">Select your units</option>
            <option value="0">Minutes & Hours</option>
            <option value="1">Kms & Miles</option>
          </select>
          {index === "0" ? <MinutesToHours /> : null}
          {index === "1" ? <KmsToMiles /> : null}
        </div>
      );
    }

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

## props

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%201.png)

저장(save) 버튼과 확인(confirm) 버튼을 만들고 싶다. 가장 기본적인 방법은 두 버튼 각각 컴포넌트로 만들어 App 컴포넌트에 렌더링시키는 것이다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const SaveBtn = () => {
      return (
        <button
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
          }}
        >
          Save Changes
        </button>
      );
    };

    const ConfirmBtn = () => {
      return (
        <button
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
          }}
        >
          Confirm
        </button>
      );
    };

    const App = () => {
      return (
        <div>
          <SaveBtn />
          <ConfirmBtn />
        </div>
      );
    };

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

버튼 개수가 1~2개일 땐 큰 문제가 없지만, 버튼의 개수가 1억개일 땐 일일이 컴포넌트로 만들 수 없다. 그리고 여러 버튼에 css style을 적용해야 한다면 모든 컴포넌트에 일일이 적용해야 한다. SaveBtn 컴포넌트와 Confirm 컴포넌트 모두 button 태그를 가지고 있으며 같은 스타일이 적용되어 있다. 다른 점은 버튼에 적힌 텍스트뿐이다.

하나의 컴포넌트로 텍스트만 다른 두 버튼을 나타낼 수 있을까?

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const Btn = () => {
      return (
        <button
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
          }}
        >
          Save Changes
        </button>
      );
    };

    const App = () => {
      return (
        <div>
          <Btn />
          <Btn />
        </div>
      );
    };

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%202.png)

이렇게 하면 Save Changes라는 텍스트를 가진 버튼만 2개 렌더링된다. 다른 것들은 그대로 두고 텍스트만 따로 설정하고 싶다. HTML 태그에 properties를 주듯이, Btn 태그에 props로 텍스트 값을 전달하면 된다.

App 컴포넌트에서 자식 컴포넌트인 Btn 컴포넌트에 text라는 property와 값을 주면 Btn 컴포넌트에서는 props라는 객체로 그것을 받고 text를 가져다가 쓸 수 있다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const Btn = (props) => {
      return (
        <button
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
          }}
        >
          {props.text}
        </button>
      );
    };

    const App = () => {
      return (
        <div>
          <Btn text="Save Changes"/>
          <Btn text="Confirm"/>
        </div>
      );
    };

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%201.png)

props에 여러 property를 전달할 수도 있다. App 컴포넌트에서 Btn 컴포넌트에 text와 big이라는 property를 전달하면 Btn 컴포넌트는 props를 다음과 같이 받는다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const Btn = (props) => {
      console.log(props);

      return (
        <button
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
            fontSize: props.big ? 18 : 12,
          }}
        >
          {props.text}
        </button>
      );
    };

    const App = () => {
      return (
        <div>
          <Btn text="Save Changes" big={true}/>
          <Btn text="Confirm" big={false}/>
        </div>
      );
    };

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%203.png)

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%204.png)

자식 컴포넌트의 property로 함수를 넘겨줄 수도 있다. 이를 통해 버튼이 눌렸을 때의 동작을 버튼마다 다르게 구현할 수 있다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const Btn = (props) => {
      const {text, changeValue} = props;

      return (
        <button
          onClick={changeValue}
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
          }}
        >
          {text}
        </button>
      );
    };

    const App = () => {
      const [value, setValue] = React.useState("Save Changes");

      const changeValue = () => setValue("Revert Changes");

      return (
        <div>
          <Btn text={value} changeValue={changeValue}/>
          <Btn text="Confirm" />
        </div>
      );
    };

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%205.png)

최초 렌더링

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%206.png)

왼쪽 버튼을 누르면 전달받은 App 컴포넌트의 changeValue 메서드가 실행되어 setValue에 의해 state value가 변경된다.

버튼 누른 후 렌더링

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%207.png)

버튼을 누른 후에는 App 컴포넌트의 state가 변경되므로 자식 컴포넌트들도 모두 다시 렌더링된다. 두 번째 버튼은 변한 것이 없는데도 렌더링된다.

Memo를 이용하면 두 번째 Btn 컴포넌트의 property가 그대로라면 부모 컴포넌트의 state가 변해도 리렌더링되지 않도록 할 수 있다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
  </body>
  <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js
    "></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    const Btn = (props) => {
      const {text, changeValue} = props;

      console.log(text, "renderd");

      return (
        <button
          onClick={changeValue}
          style={{
            backgroundColor: "tomato",
            color: "white",
            padding: "10px 20px",
            border: 0,
            borderRadius: "10px",
          }}
        >
          {text}
        </button>
      );
    };

    const MemorizedBtn = React.memo(Btn);

    const App = () => {
      const [value, setValue] = React.useState("Save Changes");

      const changeValue = () => setValue("Revert Changes");

      const confirm = () => {
        console.log("confirm");
      }

      return (
        <div>
          <MemorizedBtn text={value} changeValue={changeValue}/>
          <MemorizedBtn text="Confirm" />
        </div>
      );
    };

    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
</html>

```

버튼 누른 후 렌더링

![Untitled](24_02_29_daily_certification%206e550b2fcd574dc5aead917fdc79a004/Untitled%208.png)

버튼을 누른 후에는 App 컴포넌트의 state가 변경되어도 두 번째 버튼 컴포넌트의 property는 변한 것이 없으므로 리렌더링이 일어나지 않는다.

# Problem Solving (Algorithm & SQL)

**BOJ 16920 확장 게임**

[16920번: 확장 게임](https://www.acmicpc.net/problem/16920)

처음에는 직선으로만 Si 칸 이동할 수 있는 줄 알았다. 다른 예제는 다 맞는데 예제 5번만 틀렸다.

### 문제 이해를 잘 못한 코드

```kotlin
import java.util.*
import java.io.*

const val MAX = 10        //    최대 플레이어 수 10
const val EMPTY = '.'     //    빈 칸

class BOJ_16920 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer

    private var n = 0    //    세로 크기
    private var m = 0    //    가로 크기
    private var p = 0    //    플레이어 수

    private val s = IntArray(MAX)    //    s[i] : i번째 플레이어가 이동할 수 있는 칸의 수
    private val q = Array(MAX) { LinkedList<Pos>() }    //    q[i] : i번쨰 플레이어 bfs를 위해 사용될 queue
    private val ans = IntArray(MAX)  //    ans[i] : i번째 플레이어가 차지한 성의 개수

    private lateinit var map: Array<CharArray> //    맵

    private val castle = charArrayOf('0', '1', '2', '3', '4', '5', '6', '7', '8', '9')

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        p = tokens.nextToken().toInt()

        map = Array(n) { CharArray(m) }

        tokens = StringTokenizer(br.readLine())
        for (i in 1..p)
            s[i] = tokens.nextToken().toInt()

        for (i in 0..<n) {
            map[i] = br.readLine().toCharArray()

            for (j in 0..<m) {
                val ch = map[i][j]

                if (ch in '1'..'9') {    //    성일 경우
                    ans[ch.code - '0'.code]++    //    해당 플레이어 성 개수 증가
                    q[ch.code - '0'.code].offer(Pos(i, j))    //    해당 플레이어의 queue에 성 위치 넣기
                }
            }
        }

        bfs()

        for (i in 1..p)
            print("${ans[i]} ")
        println()
    }

    private fun bfs() {
        while (!isEnd()) {
            for (idx in 1..p) {
                val queue = q[idx]    //    idx번째 플레이어의 queue
                val size = queue.size

                for (cnt in 0..<size) {
                    val curPos = queue.poll()
                    val cy = curPos.y
                    val cx = curPos.x    //    현재 성의 위치

                    for (d in 0..<4) {    //    현재 성 기준으로 4방향 탐색
                        for (k in 1..s[idx]) {
                            val ny = cy + dy[d] * k
                            val nx = cx + dx[d] * k    //    현재 성 위치에서 d 방향으로 k칸 이동

                            if (!isIn(ny, nx))    //    해당 방향으로는 더 이상 못 갈 경우
                                break    //    다음 방향 탐색

                            if (map[ny][nx] != EMPTY)    //    해당 칸에 장애물 또는 성이 있을 경우
                                continue    //    다음 칸 탐색

                            map[ny][nx] = castle[idx]    //    빈 칸일 경우 차지하기
                            ans[idx]++    //    성 개수 증가
                            queue.offer(Pos(ny, nx))    //    queue에 삽입
                        }
                    }
                } // inner-for-end
            }   // outer-for-end
        }   // outer-while-end
    }   //  bfs-end

    //    모든 queue가 비었으면 종료
    private fun isEnd(): Boolean {
        for (i in 1..p) {
            if (q[i].isNotEmpty())
                return false
        }

        return true
    }

    private fun isIn(y: Int, x: Int): Boolean {
        return y in 0..<n && x in 0..<m
    }

    private class Pos(val y: Int, val x: Int)    //    좌표 클래스
}

fun main() {
    BOJ_16920().solve()
}
```

플레이어 i의 성 하나에서 직선으로 Si만큼 이동하는 게 아니라, 최대 Si칸만큼 이동하는 것이 문제 요구 사항이었다. 이를 반영해서 플레이어 i의 큐에 있는 위치마다 Si만큼 bfs를 하게 수정했다.

### 예제는 맞았지만 제출 시 틀린 코드

```kotlin
import java.util.*
import java.io.*

const val MAX = 10        //    최대 플레이어 수 10
const val EMPTY = '.'     //    빈 칸

class BOJ_16920 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    세로 크기
    private var m = 0    //    가로 크기
    private var p = 0    //    플레이어 수

    private val s = IntArray(MAX)    //    s[i] : i번째 플레이어가 이동할 수 있는 칸의 수
    private val q = Array(MAX) { LinkedList<Pos>() }    //    q[i] : i번쨰 플레이어 bfs를 위해 사용될 queue
    private val ans = IntArray(MAX)  //    ans[i] : i번째 플레이어가 차지한 성의 개수

    private lateinit var map : Array<CharArray> //    맵

    private val castle = charArrayOf('0', '1', '2', '3', '4', '5', '6', '7', '8', '9')

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        p = tokens.nextToken().toInt()

        map = Array(n) { CharArray(m) }

        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. p)
            s[i] = tokens.nextToken().toInt()

        for(i in 0 ..< n) {
            map[i] = br.readLine().toCharArray()

            for(j in 0 ..< m) {
                val ch = map[i][j]

                if(ch in '1' .. '9') {    //    성일 경우
                    ans[ch.code - '0'.code]++    //    해당 플레이어 성 개수 증가
                    q[ch.code - '0'.code].offer(Pos(i, j))    //    해당 플레이어의 queue에 성 위치 넣기
                }
            }
        }

        solveProblem()

        for(i in 1 .. p)
            print("${ans[i]} ")
        println()
    }

    private fun solveProblem() {
        while(!isEnd()) {
            for(idx in 1 .. p) {
                val queue = q[idx]    //    idx번째 플레이어의 queue
                    val size = queue.size

                    for(cnt in 0 ..< size) {
                        val curPos = queue.poll()
                        val cy = curPos.y
                        val cx = curPos.x    //    현재 성의 위치

                        bfs(cy, cx, idx)  //    (cy, cx)로부터 최대 s[idx]칸 이동
                    } // inner-for-end
            }   // outer-for-end
        }   // outer-while-end
    }   //  bfs-end

    private fun bfs(y : Int, x : Int, playerNum : Int) {
        val queue = LinkedList<Node>()
        val move = s[playerNum]    //    (y, x)로부터 움직일 수 있는 칸 개수

        queue.offer(Node(Pos(y, x), 0))

        while(queue.isNotEmpty()) {
            val size = queue.size

            for(cnt in 0 ..< size) {
                val cnode = queue.poll()
                val cy = cnode.pos.y
                val cx = cnode.pos.x        //    현재 위치
                val cdist = cnode.dist      //    현재 위치까지 이동한 칸 수

                if(cdist == move)    //    현재 칸까지 온 칸 수가 move이므로 더 이동 불가
                    continue

                for(d in 0 ..< 4) {
                    val ny = cy + dy[d]
                    val nx = cx + dx[d]        //    다음 위치
                    val ndist = cdist + 1      //    다음 위치까지 이동한 칸 수

                    if(isIn(ny, nx) && map[ny][nx] == EMPTY) {    //    다음 칸이 빈 칸일 경우
                        map[ny][nx] = castle[playerNum]    //    다음 칸 차지
                        ans[playerNum]++    //    해당 플레이어 성 개수 1 추가
                        queue.offer(Node(Pos(ny, nx), ndist))    //    다음 칸 탐색을 위해 queue에 추가

                        if(ndist == move)    //    움직일 수 있는 끝자락까지 왔을 경우
                            q[playerNum].offer(Pos(ny, nx))
                    }
                }
            }
        }    //    while-end
    }   //  bfs-end

    //    모든 queue가 비었으면 종료
    private fun isEnd() : Boolean {
        for(i in 1 .. p) {
            if(q[i].isNotEmpty())
                return false
        }

        return true
    }

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< m
    }

    private class Pos(val y : Int, val x : Int)    //    좌표 클래스

    private class Node(val pos : Pos, val dist : Int)   //    좌표 + 거리 클래스
}

fun main() {
    BOJ_16920().solve()
}
```

bfs를 할 때 방문 처리를 단순히 성이 있냐 없냐로만 해서 같은 위치를 더 짧은 거리로 도달할 수 있으면 거리를 갱신해서 새로 방문해야 중간에 가로막히는 일 없이 모든 칸으로부터 Si만큼 이동할 수 있다. 이 부분 로직을 좀 더 깔끔하게 수정하면 혼동이 적을 것 같지만 일단 잘 안떠올라서 visited 배열을 추가하고 큐에 넣을 때 시작 칸으로부터 현재 칸까지의 이동 칸 수도 같이 넣도록 수정했다.

참고한 반례

```
5 10 4
1 2 1 2
1........2
.....44...
......4...
2.........
....3.....

Ans
5 21 4 20
```

### 반례 보고 고쳤지만 중복 제외 안해서 틀린 코드

이미 방문했지만 더 빨리 방문할 수 있을 때의 성의 개수는 중복임에도 개수를 증가시켜줘서 틀렸다. 중복 제외 처리를 해야 한다.

```kotlin
import java.util.*
import java.io.*

const val MAX = 10        //    최대 플레이어 수 10
const val EMPTY = '.'     //    빈 칸

class BOJ_16920 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    세로 크기
    private var m = 0    //    가로 크기
    private var p = 0    //    플레이어 수

    private val s = IntArray(MAX)    //    s[i] : i번째 플레이어가 이동할 수 있는 칸의 수
    private val q = Array(MAX) { LinkedList<Pos>() }    //    q[i] : i번쨰 플레이어 bfs를 위해 사용될 queue
    private val ans = IntArray(MAX)  //    ans[i] : i번째 플레이어가 차지한 성의 개수

    private lateinit var map : Array<CharArray> //    맵
    private lateinit var visited : Array<IntArray>  //  방문 배열

    private val castle = charArrayOf('0', '1', '2', '3', '4', '5', '6', '7', '8', '9')

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        p = tokens.nextToken().toInt()

        map = Array(n) { CharArray(m) }
        visited = Array(n) { IntArray(m) { Int.MAX_VALUE } }

        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. p)
            s[i] = tokens.nextToken().toInt()

        for(i in 0 ..< n) {
            map[i] = br.readLine().toCharArray()

            for(j in 0 ..< m) {
                val ch = map[i][j]

                if(ch in '1' .. '9') {    //    성일 경우
                    ans[ch.code - '0'.code]++    //    해당 플레이어 성 개수 증가
                    q[ch.code - '0'.code].offer(Pos(i, j))    //    해당 플레이어의 queue에 성 위치 넣기
                    visited[i][j] = 0
                }
            }
        }

        solveProblem()

        for(i in 1 .. p)
            print("${ans[i]} ")
        println()
    }

    private fun solveProblem() {
        while(!isEnd()) {
            for(idx in 1 .. p) {
                val queue = q[idx]    //    idx번째 플레이어의 queue
                val size = queue.size

                for(cnt in 0 ..< size) {
                    val curPos = queue.poll()
                    val cy = curPos.y
                    val cx = curPos.x    //    현재 성의 위치

                    bfs(cy, cx, idx)  //    (cy, cx)로부터 최대 s[idx]칸 이동
                } // inner-for-end
            }   // outer-for-end
        }   // outer-while-end
    }   //  bfs-end

    private fun bfs(y : Int, x : Int, playerNum : Int) {
        val queue = LinkedList<Node>()
        val move = s[playerNum]    //    (y, x)로부터 움직일 수 있는 칸 개수

        queue.offer(Node(Pos(y, x), 0))

        while(queue.isNotEmpty()) {
            val size = queue.size

            for(cnt in 0 ..< size) {
                val cnode = queue.poll()
                val cy = cnode.pos.y
                val cx = cnode.pos.x        //    현재 위치
                val cdist = cnode.dist      //    현재 위치까지 이동한 칸 수

                if(cdist == move) {    //    현재 칸까지 온 칸 수가 move이므로 더 이동 불가
                    q[playerNum].offer(Pos(cy, cx)) //  현재 칸이 가장자리임
                    continue
                }

                for(d in 0 ..< 4) {
                    val ny = cy + dy[d]
                    val nx = cx + dx[d]        //    다음 위치
                    val ndist = cdist + 1      //    다음 위치까지 이동한 칸 수

                    //  다음 칸이 빈 칸이거나, 이미 같은 플레이어의 성이지만 더 빨리 올 수 있을 경우
                    if(isIn(ny, nx) && (map[ny][nx] == EMPTY || (map[ny][nx] == castle[playerNum] && visited[ny][nx] > ndist))) {
                        map[ny][nx] = castle[playerNum]    //    다음 칸 차지
                        visited[ny][nx] = ndist
                        ans[playerNum]++    //    해당 플레이어 성 개수 1 추가
                        queue.offer(Node(Pos(ny, nx), ndist))    //    다음 칸 탐색을 위해 queue에 추가
                    }
                }
            }
        }    //    while-end
    }   //  bfs-end

    //    모든 queue가 비었으면 종료
    private fun isEnd() : Boolean {
        for(i in 1 .. p) {
            if(q[i].isNotEmpty())
                return false
        }

        return true
    }

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< m
    }

    private class Pos(val y : Int, val x : Int)    //    좌표 클래스

    private class Node(val pos : Pos, val dist : Int)   //    좌표 + 거리 클래스
}

fun main() {
    BOJ_16920().solve()
}
```

### 겨우 겨우 맞았지만 정말 느린 코드

중복 처리까지 해서 겨우 맞긴 했지만 많이 느리다.

```kotlin
import java.util.*
import java.io.*

const val MAX = 10        //    최대 플레이어 수 10
const val EMPTY = '.'     //    빈 칸

class BOJ_16920 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    세로 크기
    private var m = 0    //    가로 크기
    private var p = 0    //    플레이어 수

    private val s = IntArray(MAX)    //    s[i] : i번째 플레이어가 이동할 수 있는 칸의 수
    private val q = Array(MAX) { LinkedList<Pos>() }    //    q[i] : i번쨰 플레이어 bfs를 위해 사용될 queue
    private val ans = IntArray(MAX)  //    ans[i] : i번째 플레이어가 차지한 성의 개수

    private lateinit var map : Array<CharArray> //    맵
    private lateinit var visited : Array<IntArray>  //  방문 배열

    private val castle = charArrayOf('0', '1', '2', '3', '4', '5', '6', '7', '8', '9')

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        p = tokens.nextToken().toInt()

        map = Array(n) { CharArray(m) }
        visited = Array(n) { IntArray(m) { Int.MAX_VALUE } }

        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. p)
            s[i] = tokens.nextToken().toInt()

        for(i in 0 ..< n) {
            map[i] = br.readLine().toCharArray()

            for(j in 0 ..< m) {
                val ch = map[i][j]

                if(ch in '1' .. '9') {    //    성일 경우
                    ans[ch.code - '0'.code]++    //    해당 플레이어 성 개수 증가
                    q[ch.code - '0'.code].offer(Pos(i, j))    //    해당 플레이어의 queue에 성 위치 넣기
                    visited[i][j] = 0
                }
            }
        }

        solveProblem()

        for(i in 1 .. p)
            print("${ans[i]} ")
        println()
    }

    private fun solveProblem() {
        while(!isEnd()) {
            for(idx in 1 .. p) {
                val queue = q[idx]    //    idx번째 플레이어의 queue
                val size = queue.size

                for(cnt in 0 ..< size) {
                    val curPos = queue.poll()
                    val cy = curPos.y
                    val cx = curPos.x    //    현재 성의 위치

                    bfs(cy, cx, idx)  //    (cy, cx)로부터 최대 s[idx]칸 이동
                } // inner-for-end
            }   // outer-for-end
        }   // outer-while-end
    }   //  bfs-end

    private fun bfs(y : Int, x : Int, playerNum : Int) {
        val queue = LinkedList<Node>()
        val move = s[playerNum]    //    (y, x)로부터 움직일 수 있는 칸 개수

        queue.offer(Node(Pos(y, x), 0))

        while(queue.isNotEmpty()) {
            val size = queue.size

            for(cnt in 0 ..< size) {
                val cnode = queue.poll()
                val cy = cnode.pos.y
                val cx = cnode.pos.x        //    현재 위치
                val cdist = cnode.dist      //    현재 위치까지 이동한 칸 수

                if(cdist == move) {    //    현재 칸까지 온 칸 수가 move이므로 더 이동 불가
                    q[playerNum].offer(Pos(cy, cx)) //  현재 칸이 가장자리임
                    continue
                }

                for(d in 0 ..< 4) {
                    val ny = cy + dy[d]
                    val nx = cx + dx[d]        //    다음 위치
                    val ndist = cdist + 1      //    다음 위치까지 이동한 칸 수

                    if(!isIn(ny, nx))   //  범위 벗어날 경우
                        continue

                    //  다음 칸이 빈 칸일 경우
                    if(map[ny][nx] == EMPTY)  {
                        map[ny][nx] = castle[playerNum]    //    다음 칸 차지
                        visited[ny][nx] = ndist
                        ans[playerNum]++    //    해당 플레이어 성 개수 1 추가
                        queue.offer(Node(Pos(ny, nx), ndist))    //    다음 칸 탐색을 위해 queue에 추가
                    }
                    //  다음 칸이 이미 플레이어가 차지한 성이지만 더 빨리 차지할 수 있을 경우
                    else if(map[ny][nx] == castle[playerNum] && visited[ny][nx] > ndist) {
                        visited[ny][nx] = ndist
                        queue.offer(Node(Pos(ny, nx), ndist))    //    다음 칸 탐색을 위해 queue에 추가
                    }
                }
            }
        }    //    while-end
    }   //  bfs-end

    //    모든 queue가 비었으면 종료
    private fun isEnd() : Boolean {
        for(i in 1 .. p) {
            if(q[i].isNotEmpty())
                return false
        }

        return true
    }

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< m
    }

    private class Pos(val y : Int, val x : Int)    //    좌표 클래스

    private class Node(val pos : Pos, val dist : Int)   //    좌표 + 거리 클래스
}

fun main() {
    BOJ_16920().solve()
}
```
