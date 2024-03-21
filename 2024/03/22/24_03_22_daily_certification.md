# 24_03_22_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 17298 오큰수, Programmers 뒤에 있는 큰 수 찾기**

[17298번: 오큰수](https://www.acmicpc.net/problem/17298)

[](https://school.programmers.co.kr/learn/courses/30/lessons/154539)

가장 뒤에 있는 수부터 시작해서 스택에 들어있는 더 뒤에 있는 수들을 확인한다. 만약 스택에 들어있는 수가 현재 수보다 작거나 같을 경우 더 큰 수가 나올 때까지 스택에서 현재 수를 제거한다. 그렇게 해서 나온 수가 현재 수의 오큰수이다. 만약 스택이 빌 때까지 제거했는데도 더 큰 수가 없으면 현재 수의 오큰수는 없다.

현재 수의 오큰수를 찾거나 없음이 판명되면 현재 수를 스택에 넣는다. 현재 수보다 뒤에 있는 수 중 현재 수보다 작은 수들은 스택에서 현재 수 아래에 있으므로 현재 수에 의해 가려지게 되므로 현재 수보다 앞에 있는 어떤 수의 오큰수도 될 수 없다.

예제 2를 통해 알아보자.

![bge21.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge21.jpeg)

처음 가장 뒤에 있는 2보다 뒤에 있는 수를 스택에서 확인한다. 당연히 뒤에는 아무 수도 없으므로 스택도 비어있고 오큰수는 -1이다. 2의 오큰수가 없다는 것을 찾았으니 스택에 넣어준다. 

뒤에서 두 번째 수인 6보다 뒤에 있는 수를 스택에서 확인한다. 2는 6보다 작으므로 스택에서 제거한다. 스택이 비었으므로 6의 오큰수도 없다. 6을 스택에 넣어준다.

3의 오큰수를 찾기 위해 스택에서 확인한다. 6이 3보다 크므로 6이 3의 오큰수이다. 3을 스택에 넣어주자.

![bge22.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge22.jpeg)

3을 스택에 넣은 상태에서 5의 오큰수를 찾기 위해 스택을 확인해보자.

![bge23.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge23.jpeg)

스택에 있는 3은 5보다 작으므로 제거하고 그 다음 스택에 있는 6이 5보다 크므로 5의 오큰수가 된다. 5를 스택에 넣어주자.

![bge24.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge24.jpeg)

1의 오큰수를 찾아보자. 5는 1보다 크고 스택에서 더 최근에 들어온 5가 더 이전에 들어온 6을 가리므로 더 가까운 오른쪽에 있는 5가 1의 오큰수가 된다. 1도 스택에 넣어주자.

![bge25.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge25.jpeg)

마지막으로 9의 오큰수를 찾아보자. 스택에 있는 어떤 수도 9보다 작다. 결국 스택에 있던 모든 수가 스택에서 제거되고 9의 오큰수는 없다. 9가 스택에 들어가긴 하지만 어차피 모든 수의 오큰수를 찾았으므로 별 의미는 없다.

**뒤에 있는 큰 수 찾기 정답 코드**

```java
import java.util.Stack;
import java.util.Arrays;

class Solution {
    public int[] solution(int[] numbers) {
        int len = numbers.length;   //  정수 배열 길이
        int[] answer = new int[len];
        Arrays.fill(answer, -1);
        
        Stack<Integer> stack = new Stack<>();
        
        //  가장 마지막 수 제외 오른쪽 끝의 수부터 확인
        for(int idx = len - 1; idx >= 0; idx--) {
            int num = numbers[idx]; //  현재 수
            
            while(!stack.isEmpty()) {                       
                int top = stack.peek(); //  스택에 가장 위에 있는 수
                
                if(num >= top)   //  현재 수가 스택 가장 위에 있는 수보다 작지 않을 경우
                    stack.pop();    //  스택에서 가장 위에 있는 수 제거
                else {  //  현재 수보다 스택 가장 위에 있는 수가 더 클 경우 그 수가 현재 수의 뒷큰수임
                    answer[idx] = top;  //  스택 가장 위에 있는 수를 뒷큰수로 삼기
                    break;  //  뒷큰수 찾았으므로 스택 더 볼 필요 없음
                }
            }
            
            stack.push(num);    //  현재 수를 스택에 넣음
        }
        
        
        return answer;
    }
}
```