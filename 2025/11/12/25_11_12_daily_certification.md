# 25_11_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2655 **가장높은탑쌓기**

[2655번: 가장높은탑쌓기](http://boj.ma/2655/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    벽돌 개수
    private static Brick[] brickArr;
    
    private static List<Integer>[] graph;
    private static int[] indegrees;
    
    private static int maxHeight = 0;
    private static int maxHeightCnt;
    private static int[] order;
    private static int[] answer;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        brickArr = new Brick[N + 1];
        graph = new ArrayList[N + 1];
        indegrees = new int[N + 1];
        order = new int[N];
        answer = new int[N];
        for(int num = 1; num <= N; num++) {
            st = new StringTokenizer(br.readLine());
            brickArr[num] = new Brick(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
            graph[num] = new ArrayList<>();
        }
        
        for(int num1 = 1; num1 <= N; num1++) {
            Brick brick1 = brickArr[num1];
            
            for(int num2 = 1; num2 <= N; num2++) {
                if(num2 != num1) {
                    Brick brick2 = brickArr[num2];
                    
                    if(brick1.area >= brick2.area && brick1.weight >= brick2.weight) {
                        graph[num1].add(num2);
                        indegrees[num2]++;
                    }
                }    
            }
        }
        
        for(int num = 1; num <= N; num++) {
            if(indegrees[num] == 0) {
                order[0] = num;
                dfs(num, brickArr[num].height, 1);
            }   
        }
        
        sb.append(maxHeightCnt).append("\n");
        for(int i = maxHeightCnt - 1; i >= 0; i--)
            sb.append(answer[i]).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dfs(int num, int heightSum, int cnt) {
        if(heightSum > maxHeight) {
            maxHeight = heightSum;
            maxHeightCnt = cnt;
            for(int i = 0; i < maxHeightCnt; i++)
                answer[i] = order[i];
        }
        
        for(int nnum : graph[num]) {
            order[cnt] = nnum;
            dfs(nnum, heightSum + brickArr[nnum].height, cnt + 1);
        }
    }
    
    private static class Brick {
        public int area;        //    밑면 넓이
        public int height;      //    높이
        public int weight;      //    무게
        
        public Brick(int area, int height, int weight) {
            this.area = area;
            this.height = height;
            this.weight = weight;
        }
    }
}    //    Main-class-end
```