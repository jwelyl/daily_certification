# 26_04_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24492 Cow Frisbee

[24492번: Cow Frisbee](http://boj.ma/24492/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.ArrayDeque;
import java.util.Arrays;

public class Main {
  private static final int NONE = -1;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    Deque<Element> stack = new ArrayDeque<>();

    int[] heights = new int[n];
    //    leftLargers[i] : j > i일 때, heights[j] > heights[i]가 되는 최소 j, 없으면 NONE
    int[] leftLargers = new int[n];
    //    rightLargers[i] : i < j일 때, heights[j] > heights[i]가 되는 최대 j, 없으면 NONE
    int[] rightLargers = new int[n];

    long answer = 0;
    
    Arrays.fill(leftLargers, NONE);
    Arrays.fill(rightLargers, NONE);

    StringTokenizer st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      heights[i] = Integer.parseInt(st.nextToken());

    for(int i = 0; i < n; i++) {
      while(!stack.isEmpty() && stack.peekLast().height < heights[i])
        stack.pollLast();

      if(!stack.isEmpty())
        leftLargers[i] = stack.peekLast().idx;
      stack.offerLast(new Element(heights[i], i));
    }
    stack.clear();
    for(int i = n - 1; i >= 0; i--) {
      while(!stack.isEmpty() && stack.peekLast().height < heights[i])
        stack.pollLast();

      if(!stack.isEmpty())
        rightLargers[i] = stack.peekLast().idx;
      stack.offerLast(new Element(heights[i], i));
    }

    for(int i = 0; i < n; i++) {
      if(leftLargers[i] != NONE)
        answer += (i - leftLargers[i] + 1);
      if(rightLargers[i] != NONE)
        answer += (rightLargers[i] - i + 1);
    }

    System.out.println(answer);
  }    //    main-end

  private static class Element {
    public int height;
    public int idx;

    public Element(int height, int idx) {
      this.height = height;
      this.idx = idx;
    }
  }
}    //    Main-class-end
```