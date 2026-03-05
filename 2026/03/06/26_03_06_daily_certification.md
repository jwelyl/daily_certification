# 26_03_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1464 뒤집기 3

[1464번: 뒤집기 3](http://boj.ma/1464/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    char[] input = br.readLine().toCharArray();

    Deque<Character> deque = new ArrayDeque<>();
    
    for(int i = 0; i < input.length; i++) {
      if(!deque.isEmpty() && deque.peekLast() < input[i])
        deque.offerFirst(input[i]);
      else
        deque.offerLast(input[i]);
    }

    while(!deque.isEmpty())
      System.out.print(deque.pollLast());
    System.out.println();
  } //	main-end
} //	Main-class-end
```