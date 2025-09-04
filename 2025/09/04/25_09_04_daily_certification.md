# 25_09_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20926 얼음 미로

[20926번: 얼음 미로](http://boj.ma/20926/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.PriorityQueue;

public class Main {
    private static final int INF = 9 * 500 * 500 + 1;
    private static final char SRC = 'T';
    private static final char DST = 'E';
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int W;
    private static int H;
    
    private static char[][] map;
    private static int sy;
    private static int sx;
    private static int ey;
    private static int ex;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        W = Integer.parseInt(st.nextToken());
        H = Integer.parseInt(st.nextToken());
        
        map = new char[H][W];
        for(int y = 0; y < H; y++) {
            map[y] = br.readLine().toCharArray();
            for(int x = 0; x < W; x++) {
                if(map[y][x] == SRC) {
                    sy = y;
                    sx = x;
                }
                else if(map[y][x] == DST) {
                    ey = y;
                    ex = x;
                }
            }
        }
        
        System.out.println(dijkstra());
    }    //    main-end
    
    private static int dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        int[] dist = new int[H * W];
        int src = sy * W + sx;
        int dst = ey * W + ex;
        
        Arrays.fill(dist, INF);
        
        pq.offer(new Node(src, 0));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cy = cv / W;
            int cx = cv % W;
            int cc = cur.cost;
            
            if(dist[cv] < cc)
                continue;
            
            //    (cy, cx)에서 이동할 수 있는 칸 찾기
            for(int d = 0; d < 4; d++) {
                int ncost = 0;    //    d 방향으로 미끄러져서 도착할 수 있는 칸까지 거리
                
                for(int k = 1; ; k++) {
                    int ny = cy + dy[d] * k;
                    int nx = cx + dx[d] * k;    //    d 방향으로 미끄러져서 도착한 칸
                    
                    if(!isIn(ny, nx))    //    도착한 칸이 절벽일 경우
                        break;
                    
                    if(map[ny][nx] == DST) {    //    출구에 도달할 경우    
                        if(cc + ncost < dist[dst]) {    //    출구까지 최단거리가 갱신될 경우
                            dist[dst] = cc + ncost;
                            pq.offer(new Node(dst, dist[dst]));
                        }
                        
                        break;
                    }
                    
                    //    d 방향으로 가면 절벽이나 구멍으로 빠질 경우, 또는 바위라서 더 이상 이동할 수 없을 경우
                    if(isBoundary(ny, nx, d) || map[ny][nx] == 'H' || map[ny][nx] == 'R')
                        break;
                    
                    ncost += (map[ny][nx] == SRC || map[ny][nx] == DST) ? 0 : map[ny][nx] - '0';
                        
                    //    바위에 의해 멈출 수 있는 칸일 경우
                    if(isEnd(ny, nx, d)) {
                        int nv = ny * W + nx;
                        
                        if(cc + ncost < dist[nv]) {
                            dist[nv] = cc + ncost;
                            pq.offer(new Node(nv, dist[nv]));
                        }
                    }
                }
            }
        }
        
        return dist[dst] == INF ? -1 : dist[dst];
    }

    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    //    d 방향으로 미끄러져서 (y, x)에 도달했을때 그 다음 칸이 절벽 아래일 경우
    private static boolean isBoundary(int y, int x, int d) {
        int ny = y + dy[d];
        int nx = x + dx[d];
        
        return !isIn(ny, nx);
    }
    
    //    d 방향으로 미끄러질 때 바위에 의해 (y, x)에서 멈출 수 있을 경우
    private static boolean isEnd(int y, int x, int d) {
        int ny = y + dy[d];
        int nx = x + dx[d];
        
        return map[ny][nx] == 'R'; 
    }

    private static boolean isIn(int y, int x) {
        return (0 <= y && y < H) && (0 <= x && x < W);
    }

        private static class Node implements Comparable<Node> {
        public int vertex;
        public int cost;
        
        public Node(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```