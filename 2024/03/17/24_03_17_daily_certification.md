# 24_03_17_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 10750 Censoring**

[10750번: Censoring](https://www.acmicpc.net/problem/10750)

주어진 문자열을 str, 제거할 문자열을 text라고 하자.

예제 1을 기준으로 설명해보겠다.

![ex1.jpeg](24_03_17_daily_certification%20aa5e5ab0b829451a86b8c20a9c6a2cdc/ex1.jpeg)

str의 문자를 순서대로 1개씩 확인하면서 StringBuilder sb에 추가해준다. sb의 길이가 text의 길이 이상일 때는 sb에 저장된 문자들 중 끝 부분이 text와 일치하는지 확인한다. text와 일치하면 text와 일치하는 부분만 제거하면된다.

![ex1_1.jpeg](24_03_17_daily_certification%20aa5e5ab0b829451a86b8c20a9c6a2cdc/ex1_1.jpeg)

색칠된 문자가 str의 문자 중 sb에 저장된 문자이고, 색칠되지 않은 문자는 아직 sb에 추가되지 않은 문자이다. 

sb의 길이가 text의 길이인 3 이상일 때부터 sb의 끝부분과 text를 비교한다. 위의 그림은 비교 결과 text와 같지 않을 경우 제거가 일어나지 않는 경우를 보여준다. 제거 없이 str의 문자를 계속 추가해주면 된다.

![ex1_2.jpeg](24_03_17_daily_certification%20aa5e5ab0b829451a86b8c20a9c6a2cdc/ex1_2.jpeg)

sb의 끝 부분이 text와 일치할 경우 sb에서 해당 부분만 제거하고 계속 str의 남은 문자를 추가하면서 반복하면 결국 text가 모두 제거된 결과만 남는다.

str의 길이는 최대 10^6이고 text의 길이는 100이다. 매번 str의 문자를 추가할 때마다 text 길이만큼 비교를 한다고 쳐도 O(10^8)의 시간복잡도로 해결할 수 있다. 

**코드**

```java
import java.io.*;

public class Main {
    private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private final static StringBuilder sb = new StringBuilder();
    
    private static char[] str;
    private static char[] text;
    
    public static void main(String[] args) throws IOException {
        str = br.readLine().toCharArray();
        text = br.readLine().toCharArray();
        
        for(char ch : str) {
            sb.append(ch);    //    sb에 추가
            
            if(sb.length() >= text.length) {    //    sb의 길이가 text의 길이 이상일 경우 text가 포함되었는지 확인
               boolean find = true;    //    sb 안에 text가 포함될 경우 true
               
               //  sb의 끝과 text를 비교
               for(int i = text.length - 1; i >= 0; i--) {
                   //    sb의 끝과 text가 일치하지 않을 경우
                   if(sb.charAt(sb.length() - text.length + i) != text[i]) {
                       find = false;
                       break;
                   }
               }
               
               if(find)    //    sb의 끝과 text가 일치할 경우
                   sb.delete(sb.length() - text.length, sb.length());
            }
        }
        
        System.out.println(sb);
    }    //   main-end
}   //   Main-class-end
```

sb의 역할을 stack1이 대신하고, text 제거를 위해 stack2를 도입하면 StringBuilder 대신 stack으로도 구현할 수 있다. 결과 출력을 위해서 StringBuilder를 사용하는 건 별개로 말이다.

```java
import java.io.*;
import java.util.*;

public class Main {
    private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private final static StringBuilder sb = new StringBuilder();
    
    private static char[] str;
    private static char[] text;
    
    private final static Stack<Character> stack1 = new Stack<>();    //    text 제거 후 남은 문자
    private final static Stack<Character> stack2 = new Stack<>();    //    text 제거를 위해 필요한 문자
    
    public static void main(String[] args) throws IOException {
        str = br.readLine().toCharArray();
        text = br.readLine().toCharArray();
        
        for(char ch : str) {
            stack1.push(ch);    //    str의 문자 추가
            
            boolean find = true;    //    현재까지 stack1 안에서 text 찾을 경우 true 
            
            if(stack1.size() >= text.length) {
                for(int i = text.length - 1; i >= 0; i--) {
                    char pop = stack1.pop();    //    stack1에서 꺼냄    
                    stack2.push(pop);    //    stack1에서 꺼낸 문자를 stack2에 넣음 
                    
                    if(text[i] != pop) {    //    불일치할 경우
                        find = false;
                        break;
                    }
                }
            }
            
            if(!find) {    //    끝 부분이 text와 불일치할 경우
                while(!stack2.isEmpty())
                    stack1.push(stack2.pop());    //    stack1에서 제거했던 문자 모두 복구
            }
            else stack2.clear();    //    stack2 비우기
        }
        
        while(!stack1.isEmpty())    //    stack1에 남은 문자 모두 sb로 옮기기
            sb.append(stack1.pop());
        
        System.out.println(sb.reverse());    //    뒤집어서 출력
    }    //   main-end
}   //   Main-class-end
```