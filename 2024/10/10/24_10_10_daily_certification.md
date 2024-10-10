# 24_10_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16929 **Two Dots**

[](https://www.acmicpc.net/problem/16929)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;    //    격자 크기
    
    private static char[][] board;
    private static boolean[][] visited;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        board = new char[N][M];
        visited = new boolean[N][M];
        
        for(int r = 0; r < N; r++)
            board[r] = br.readLine().toCharArray();
        
        for(int r = 0; r < N; r++) {
            for(int c = 0; c < M; c++) {
                if(!visited[r][c]) {
                    if(dfs(r, c, -1, -1)) {
                        System.out.println("Yes");
                        return;
                    }
                }
            }
        }
        
        System.out.println("No");
    }    //    main-end
    
    private static boolean dfs(int y, int x, int py, int px) {
        visited[y][x] = true;    //    현재 칸 방문 처리
        
        for(int d = 0; d < 4; d++) {
            int ny = y + dy[d];
            int nx = x + dx[d];    //    다음 칸
            
            if(isIn(ny, nx) && board[ny][nx] == board[y][x]) {    //    현재 칸과 다음 칸의 점이 같은 색일 경우
                if(!visited[ny][nx]) {
                    if(dfs(ny, nx, y, x))
                        return true;
                }
                else {    //    (ny, nx)가 이미 방문한 칸일 경우
                    if(ny != py || nx != px)    //    이미 방문한 칸인데, 직전 칸이 아닐 경우, 사이클 발생
                        return true;
                }
            }
        }
        
        return false;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

### BOJ 1202 보석 도둑

[](https://www.acmicpc.net/problem/1202)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.Collections;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    보석 개수
    private static int K;    //    가방 개수
    private static Gem[] gems;    //    보석 정보
    private static int[] bags;    //    가방 무게 정보
    
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
    
    private static long ans = 0L;    //    담을 수 있는 보석 가치 합의 최대
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        gems = new Gem[N];
        bags = new int[K];
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int m = Integer.parseInt(st.nextToken());
            int v = Integer.parseInt(st.nextToken());
            
            gems[i] = new Gem(m, v);
        }
        
        for(int k = 0; k < K; k++)
            bags[k] = Integer.parseInt(br.readLine());
        
        Arrays.sort(gems);    //    보석 가벼운 순으로 정렬
        Arrays.sort(bags);    //    가방 담을 수 있는 무게 적은 순으로 정렬
        
        int idx = 0;    //    보석 index
        
        for(int k = 0; k < K; k++) {
            int m = bags[k];    //    k번째 가방에 담을 수 있는 보석 무게
            
            while(idx < N && gems[idx].m <= m)    //    k번째 가방에 담을 수 있는 보석일 경우
                pq.offer(gems[idx++].v);    //    pq에 넣기
            
            if(!pq.isEmpty())    //    현재 가방에 담을 수 있는 보석이 있을 경우
                ans += pq.poll();    //    그 중 가치 가장 큰 보석 꺼내서 가치 더하기
        }
        
        System.out.println(ans);
    }    //    main-end
    
    private static class Gem implements Comparable<Gem> {
        public final int m;    //    보석 무게
        public final int v;    //    보석 가치
        
        public Gem(int m, int v) {
            this.m = m;
            this.v = v;
        }
        
        @Override
        public int compareTo(Gem other) {    //    보석 무게 순 정렬 (가벼운 보석부터)
            return Integer.compare(this.m, other.m);
        }
    }
}    //    Main-class-end
```