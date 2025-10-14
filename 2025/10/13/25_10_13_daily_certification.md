# 25_10_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24492 **Cow Frisbee**

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
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static final Deque<Element> stack = new ArrayDeque<>();
    
    private static int N;
    private static int[] heights;
    //    leftLargers[i] : j > i일 때, heights[j] > heights[i]가 되는 최소 j, 없으면 NONE
    private static int[] leftLargers;
    //    rightLargers[i] : i < j일 때, heights[j] > heights[i]가 되는 최대 j, 없으면 NONE
    private static int[] rightLargers;
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        heights = new int[N];
        leftLargers = new int[N];
        rightLargers = new int[N];
        Arrays.fill(leftLargers, NONE);
        Arrays.fill(rightLargers, NONE);
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            heights[i] = Integer.parseInt(st.nextToken());
        
        for(int i = 0; i < N; i++) {
            while(!stack.isEmpty() && stack.peekLast().height < heights[i])
                stack.pollLast();
            
            if(!stack.isEmpty())
                leftLargers[i] = stack.peekLast().idx;
            stack.offerLast(new Element(heights[i], i));
        }
        stack.clear();
        for(int i = N - 1; i >= 0; i--) {
            while(!stack.isEmpty() && stack.peekLast().height < heights[i])
                stack.pollLast();
            
            if(!stack.isEmpty())
                rightLargers[i] = stack.peekLast().idx;
            stack.offerLast(new Element(heights[i], i));
        }
        
        for(int i = 0; i < N; i++) {
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