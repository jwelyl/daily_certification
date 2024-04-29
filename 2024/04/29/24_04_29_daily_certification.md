# 24_04_29_daily_certification

# React

## React Hooks

[리액트 Hooks 10분만에 사용법 배우기 | Learn How to use React Hooks in 10 min](https://www.youtube.com/watch?v=yS-BU6eYUDE)

- Functional Component에서도 state를 가질 수 있게 해줌
- Functional Programming

## useState

Functional Component에서도 state를 사용하고 관리할 수 있게 해준다.

**App.js (class component)**

```jsx
import React, { Component } from "react";

class App extends Component {
  state = {
    count: 0,
  };

  modify = (n) => {
    this.setState({
      count: n,
    });
  };

  render() {
    const { count } = this.state;
    return (
      <>
        <div>{count}</div>
        <button onClick={() => this.modify(count + 1)}>Increment</button>
      </>
    );
  }
}

export default App;
```

**App.js (functional component)**

```jsx
import {useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);

  return (
    <>
      <div>{count}</div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </>
  )
}

export default App;

```

class component에서 직접 state를 관리해야 했던 것을 functional component에서는 useState가 편하게 해준다.

```jsx
import { useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  const [email, setEmail] = useState("");

  const updateEmail = (e) => {
    const {
      target: { value },
    } = e;
    setEmail(value);
  };

  return (
    <>
      <div>{count}</div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <input placeholder="Email" value={email} onChange={updateEmail} />
    </>
  );
};

export default App;

```

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 세 수의 합**

[https://www.codetree.ai/missions/8/problems/sum-of-three-num/description](https://www.codetree.ai/missions/8/problems/sum-of-three-num/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-three-num/description)

이분탐색의 경우 두 수의 합 문제와 동일한 방법으로 해결 가능하다. n이 최대 1,000이므로 O(nlogn)으로 정렬 후, 앞의 두  수에 대해서 두 수의 보수를 이분탐색으로 찾으면 된다. 따라서 O(n^2logn)으로 해결 가능하다.

원래 문제의 의도인 해시맵으로 풀이하기 위해서는 조금 아이디어가 필요하다

주어진 수를 첫 번째 수부터 확인하며 세 수 중 첫 번째 수로 정한다. 이 때 해시맵에 저장해둔 수의 등장 횟수를 1 감소시켜 중복으로 세는 일이 없도록 한다. 그리고 첫 번째 수 이전에 등장하는 수 중 두 번째 수를 정하고 이를 통해 보수 target을 정한 다음에 target의 개수를 해시맵으로 구하면 된다.

**코드 (이분탐색 풀이)**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  수 개수
    private static int k;   //  세 수 합 k

    private static final List<Integer> nums = new ArrayList<Integer>();      //  수
    private static long ans = 0L;   //  세 수 합 k 되는 조합 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            nums.add(Integer.parseInt(tokens.nextToken()));

        Collections.sort(nums); //  오름차순 정렬

        for(int i = 0; i < n - 2; i++) {
            for(int j = i + 1; j < n - 1; j++) {
                int num1 = nums.get(i);
                int num2 = nums.get(j);
                int target = k - num1 - num2;

                if(target >= num2)  //  찾으려는 수가 num2보다 오른쪽에 존재할 경우
                    ans += countOfNums(target, j + 1, n - 1);
            }
        }

        System.out.println(ans);
    }   //  main-end

    private static int countOfNums(int target, int start, int end) {
        int startIdx = leftBound(target, start, end);   //  nums에서 target의 가장 왼쪽 index
        int endIdx = rightBound(target, start, end);    //  nums에서 target의 가장 오른쪽 index

        if(endIdx == -1 || startIdx == -1)  //  둘 중 하나라도 -1이면
            return 0;   //  target이 존재하지 않음

        return endIdx - startIdx + 1;
    }

    //  [start, end]에서 가장 처음 나타나는 target의 index
    private static int leftBound(int target, int start, int end) {
        int ret = -1;

        while(start <= end) {
            int mid = (start + end) / 2;

            if(nums.get(mid) >= target) {   //  target보다 mid값이 더 크거나 같을 경우
                if(nums.get(mid) == target) //  target과 일치할 경우
                    ret = mid;              //  일단 mid 저장

                end = mid - 1;  //  더 왼쪽 찾아봐야 함
            }
            else    //  target보다 mid값이 더 작을 경우 
                start = mid + 1;    //  더 오른쪽 찾아봐야 함
        }

        return ret;
    }

    //  [start, end]에서 가장 나중에 나타나는 target의 index
    private static int rightBound(int target, int start, int end) {
        int ret = -1;

        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(nums.get(mid) <= target) {   //  target보다 mid값이 더 작거나 같을 경우
                if(nums.get(mid) == target) //  target과 일치할 경우
                    ret = mid;              //  일단 mid 저장

                start = mid + 1;   //  더 오른쪽 찾아봐야 함
            }
            else    //  target보다 mid값이 더 클 경우 
                end = mid - 1;    //  더 왼쪽 찾아봐야 함
        }

        return ret;
    }
}   //  Main-class-end
```

**코드 (해시맵 풀이)**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  수 개수
    private static int k;   //  세 수 합 k

    private static final List<Integer> nums = new ArrayList<>();
    private static final Map<Integer, Integer> numMap = new HashMap<>();      //  Key : 수, Value : 수의 등장 횟수
    private static long ans = 0L;   //  세 수 합 k 되는 조합 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            int num = Integer.parseInt(tokens.nextToken());
            nums.add(num);

            numMap.put(num, numMap.getOrDefault(num, 0) + 1);    //  num의 등장 횟수 증가
        }

        for(int i = 0; i < n; i++) {
            int num1 = nums.get(i); //  첫 번째 수

            if(numMap.containsKey(num1)) {
                numMap.put(num1, numMap.get(num1) - 1); //  등장 횟수 1 감소 (중복해서 세지 않도록)

                if(numMap.get(num1) == 0)   //  더 이상 등장하지 않을 경우
                    numMap.remove(num1);    //  num1을 해시맵에서 제거
            }
        
            for(int j = 0; j < i; j++) {
                int num2 = nums.get(j); //  두 번째 수
                int target = k - num1 - num2;
                
                if(numMap.containsKey(target))
                    ans += numMap.get(target);
            }
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```