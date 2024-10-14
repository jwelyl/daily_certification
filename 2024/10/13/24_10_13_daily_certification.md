# 24_10_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17485 **진우의 달 여행 (Large)**

[](https://www.acmicpc.net/problem/17485)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int INF = 100 * 1_000 * 1_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[][] map;
    private static int[][][] dp;
    
    private static int ans = INF;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new int[N + 1][M + 2];
        dp = new int[N + 1][M + 2][3];
        
        for(int r = 1; r <= N; r++) {
            st = new StringTokenizer(br.readLine());
            for(int c = 0; c < M; c++) {
                map[r][c] = Integer.parseInt(st.nextToken());
                dp[r][c][0] = INF;
                dp[r][c][1] = INF;
                dp[r][c][2] = INF;
            }
        }
        
        for(int r = 0; r < N; r++) {
            for(int c = 0; c < M; c++) {
                int nr = r + 1;
                int nc = c - 1;
                
                if(isIn(nr, nc))
                    dp[nr][nc][0] = Math.min(dp[nr][nc][0], Math.min(dp[r][c][1], dp[r][c][2]) + map[nr][nc]);
                
                nc = c;
                
                if(isIn(nr, nc))
                    dp[nr][nc][1] = Math.min(dp[nr][nc][1], Math.min(dp[r][c][0], dp[r][c][2]) + map[nr][nc]);
                
                nc = c + 1;
                
                if(isIn(nr, nc))
                    dp[nr][nc][2] = Math.min(dp[nr][nc][2], Math.min(dp[r][c][0], dp[r][c][1]) + map[nr][nc]);
            }
        }
        
        for(int c = 0; c < M; c++) {
            for(int d = 0; d < 3; d++)
                ans = Math.min(ans, dp[N][c][d]);
        }
        
        System.out.println(ans);
    }    //    main-end

    private static boolean isIn(int y, int x) {
        return (0 <= y && y <= N) && (0 <= x && x < M);
    }
}    //    Main-class-end

```

### BOJ 19598 **최소 회의실 개수**

[](https://www.acmicpc.net/problem/19598)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    회의 개수
    private static final List<Meeting> meetings = new ArrayList<>();
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();
    
    private static int ans = 0;    //    최소 회의실 개수
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int m = 0; m < N; m++) {
            st = new StringTokenizer(br.readLine());
            
            meetings.add(new Meeting(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
        }
        
        Collections.sort(meetings);
        
        for(Meeting meeting : meetings) {
            //    meeting이 가장 빨리 끝나는 회의보다 늦게 끝나는 경우
            while(!pq.isEmpty() && pq.peek() <= meeting.start)
                pq.poll();    //    가장 빨리 끝나는 회의 pq에서 제거
            
            pq.offer(meeting.end);    //    현재 회의의 끝나는 시간을 pq에 삽입
            ans = Math.max(ans, pq.size());
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static class Meeting implements Comparable<Meeting> {
        public int start;
        public int end;
        
        public Meeting(int start, int end) {
            this.start = start;
            this.end = end;
        }
        
        @Override
        public int compareTo(Meeting other) {
            //    빨리 시작하는 회의  순으로 정렬
            return Integer.compare(this.start, other.start);
        }
    }
}    //    Main-class-end
```