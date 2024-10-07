# 24_10_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1655 가운데를 말해요

[](https://www.acmicpc.net/problem/1655)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Collections;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int N;    //    정수 개수
    
    //    maxHeap에는 지금까지 들어있는 수 X개 중 작은 수 ceil(X / 2)개가 존재
    //    maxHeap의 top에는 작은 수들 중 가장 큰 수가 들어있음 (이것이 가운데 수)
    //    minHeap에는 지금까지 들어있는 수 X개 중 큰 수 floor(X / 2)개가 존재
    //    minHeap의 top에는 큰 수들 중 가장 작은 수가 들어있음 (가운데 수의 바로 다음 수)
    private static final PriorityQueue<Integer> minHeap = new PriorityQueue<>();    
    private static final PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        maxHeap.offer(Integer.parseInt(br.readLine()));
        sb.append(maxHeap.peek()).append("\n");
        
        for(int i = 1; i < N; i++) {
            int num = Integer.parseInt(br.readLine());
            
            if(minHeap.size() == maxHeap.size()) {    //    maxHeap에 넣을 차례
                if(num > minHeap.peek()) {    //    num이 minHeap에서 가장 작은 수보다 클 경우
                    maxHeap.offer(minHeap.poll());    //    minHeap에서 가장 작은 수를 빼서 maxHeap에 넣음
                    minHeap.offer(num);
                }
                else
                    maxHeap.offer(num);
            }
            else {    //    minHeap에 넣을 차례
                if(num < maxHeap.peek()) {    //    num이 maxHeap에서 가장 큰 수보다 작을 경우
                    minHeap.offer(maxHeap.poll());
                    maxHeap.offer(num);
                }
                else
                    minHeap.offer(num);
            }
            
            sb.append(maxHeap.peek()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1937 **욕심쟁이 판다**

[](https://www.acmicpc.net/problem/1937)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final int NONE = -1;    //    아직 정해지지 않은 칸의 값
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    N * N 크기 격자
    
    private static int[][] map;
    private static int[][] dp;    //    dp[y][x] : (y, x)에서 출발했을 때 최대로 갈 수 있는 칸 개수 ((y, x)를 포함함)
    
    private static int max = -1;    //    정답
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        map = new int[N][N];
        dp = new int[N][N];
        
        for(int y = 0; y < N; y++) {
            Arrays.fill(dp[y], NONE);
            st = new StringTokenizer(br.readLine());
            for(int x = 0; x < N; x++)
                map[y][x] = Integer.parseInt(st.nextToken());
        }
        
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < N; x++)
                max = Math.max(max, dfs(y, x));
        }
        
        System.out.println(max);
    }
    
    //    (y, x)에서 출발했을때 갈 수 있는 최대 칸 개수
    private static int dfs(int y, int x) {
        if(dp[y][x] != NONE)    //    이미 알려진 경우
            return dp[y][x];
        
        dp[y][x] = 1;    //    일단 현재 칸 갈수 있는 칸임
        
        for(int d = 0; d < 4; d++) {    //    주변 4칸 확인
            int ny = y + dy[d];
            int nx = x + dx[d];    //    주변 칸
        
            if(isIn(ny, nx) && map[y][x] < map[ny][nx])    //    주변 칸이 범위를 벗어나지 않고 현재 칸보다 클 경우
                dp[y][x] = Math.max(dp[y][x], 1 + dfs(ny, nx));
        }
        
        return dp[y][x];
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x ) {
        return (0 <= y && y < N) && (0 <= x && x < N);
    }
}    //    Main-class-end
```