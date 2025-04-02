# 25_04_02_daily_certification

```
[#092 koreii] 데일리인증 20250402
0. 상반기 일자리 재창출 현황
- 면접 대비 말하기 연습
- 기술 관련 질문 최대한 연습하는 중
1. 코딩 테스트 대비 복습 문제 풀이
- Dijkstra, DP (BOJ 11909 배열 탈출)
```

# Problem Solving (Algorithm & SQL)

### BOJ 11909 배열 탈출

[11909번: 배열 탈출](http://boj.ma/11909/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int[][] map;
    private static int[][] dist;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        map = new int[N][N];
        dist = new int[N][N];
        for(int i = 0; i < N; i++) {
            Arrays.fill(dist[i], Integer.MAX_VALUE);
            st = new StringTokenizer(br.readLine());
            for(int j = 0; j < N; j++)
                map[i][j] = Integer.parseInt(st.nextToken());
        }
        
        dijkstra();

        System.out.println(dist[N - 1][N - 1]);
    }    //    main-end
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        dist[0][0] = 0;    //    (0, 0)에서 (0, 0)까지 최단 거리
        pq.offer(new Node(0, 0, dist[0][0]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cy = cur.y;
            int cx = cur.x;
            int cc = cur.cost;
            
            if(dist[cy][cx] < cc)    //    (cy, cx)를 거쳐서 개선될 여지가 없으면
                continue;
            
            for(int d = 0; d < 2; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                int nc = cc;    //    (cy, cx)에서 (ny, nx)까지 가는데 필요한 비용
                
                if(isIn(ny, nx)) {
                    if(map[ny][nx] >= map[cy][cx])    //    (ny, nx)까지 가는데 추가 비용 발생할 경우
                        nc += map[ny][nx] + 1 - map[cy][cx];                    
                    
                    if(nc < dist[ny][nx]) {
                        dist[ny][nx] = nc;
                        pq.offer(new Node(ny, nx, dist[ny][nx]));
                    }
                }
            }
        }
    }
    
    private static final int[] dy = {0, 1};
    private static final int[] dx = {1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < N);
    }
    
    private static class Node implements Comparable<Node> {
        public int y;
        public int x;
        public int cost;
        
        public Node(int y, int x, int cost) {
            this.y = y;
            this.x = x;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```