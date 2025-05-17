# 25_05_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26591 Rain Boots

[26591번: Rain Boots](http://boj.ma/26591/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;

public class Main {
    private static final int INF = 8 * 8 + 1;
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();

    private static final char[][] map = new char[8][8];
    private static final int[][] dist = new int[8][8];

    private static int sy;
    private static int sx;
    private static int ey;
    private static int ex;

    public static void main(String[] args) throws IOException {
        while (true) {
            for (int y = 0; y < 8; y++) {
                String line = br.readLine();
                if (line == null) {
                    System.out.print(sb);
                    return;
                }
                map[y] = line.toCharArray();
                Arrays.fill(dist[y], INF);
                for (int x = 0; x < 8; x++) {
                    if (map[y][x] == 'S') {
                        sy = y;
                        sx = x;
                        dist[sy][sx] = 0;
                    } else if (map[y][x] == 'E') {
                        ey = y;
                        ex = x;
                    }
                }
            }

            sb.append(dijkstra()).append("\n");

            // 다음 입력 구분자 확인
            String next = br.readLine();
            if (next == null) {
                System.out.print(sb);
                break;
            }
        }
    } // main-end

    private static int dijkstra() {
        PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[2], n2[2]));
        
        for(int y = 0; y < 8; y++)
        	Arrays.fill(dist[y], INF);
        dist[sy][sx] = 0;
        
        pq.offer(new int[] { sy, sx, dist[sy][sx] });

        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int cy = cur[0];
            int cx = cur[1];
            int cc = cur[2];

            if (dist[cy][cx] < cc)
                continue;

            for (int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                if (isIn(ny, nx)) {
                    int nc = cc + (map[ny][nx] == 'M' ? 1 : 0);
                    if (nc < dist[ny][nx]) {
                        dist[ny][nx] = nc;
                        pq.offer(new int[] { ny, nx, nc });
                    }
                }
            }
        }

        return dist[ey][ex];
    }

    private static final int[] dy = { 0, 1, 0, -1 };
    private static final int[] dx = { 1, 0, -1, 0 };

    private static boolean isIn(int y, int x) {
        return (0 <= y && y < 8) && (0 <= x && x < 8);
    }
} // Main-class-end
```