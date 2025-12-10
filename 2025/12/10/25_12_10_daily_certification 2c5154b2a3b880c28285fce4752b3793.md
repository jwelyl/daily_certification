# 25_12_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15926 **현욱은 괄호왕이야!!**

[15926번: 현욱은 괄호왕이야!!](http://boj.ma/15926/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());
    char[] parentheses = br.readLine().toCharArray();

    Deque<Integer> stack = new ArrayDeque<>();
    int answer = 0;

    stack.offerLast(-1);

    for (int i = 0; i < n; i++) {
      char parenthesis = parentheses[i];

      if (parenthesis == '(')
        stack.offerLast(i);
      else {
        stack.pollLast();

        if(!stack.isEmpty())
          answer = Math.max(answer, i - stack.peekLast());
        else
          stack.offerLast(i);
      }
    }

    System.out.println(answer);
  } //  main-end
} //  Main-class-end

```