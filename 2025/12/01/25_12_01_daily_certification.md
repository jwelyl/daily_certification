# 25_12_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8017 Parcel

[8017: Parcel](http://boj.ma/8017/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        int[][] mat = new int[n][n];
        int[] hArr = new int[n];    //    hArr[col] : col열에서 직사각형 높이
        int answer = 0;
        
        for(int y = 0; y < n; y++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            for(int x = 0; x < n; x++) {
                mat[y][x] = Integer.parseInt(st.nextToken());
                if(mat[y][x] == 0) //    직사각형에 포함될 경우
                    hArr[x]++;
                else
                    hArr[x] = 0;   //    x = 0에서 이전에 이어지던 직사각형과 단절됨
            }
            
            Deque<Integer> stack = new ArrayDeque<>();
            for(int x = 0; x < n; x++) {
                int height = hArr[x];
                
                while(!stack.isEmpty() && height < hArr[stack.peekLast()]) {
                    int prevX = stack.pollLast();
                    int prevH = hArr[prevX];
                    int width = stack.isEmpty() ? x : x - stack.peekLast() - 1;
                    
                    answer = Math.max(answer, prevH * width);
                }
                
                stack.offerLast(x);
            }
            
            while(!stack.isEmpty()) {
                int height = hArr[stack.pollLast()];
                int width = stack.isEmpty() ? n : n - stack.peekLast() - 1;
                answer = Math.max(answer, height * width);
            }
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```