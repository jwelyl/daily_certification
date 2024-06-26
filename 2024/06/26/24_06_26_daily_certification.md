# 24_06_26_daily_certification

# Network

# OSI 7 Layer

# Identifier

![identifier.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/OSI%207%20Layer%20cd56d4399f544746b56994ccba2cc09e/identifier.jpeg)

![identifier2.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/OSI%207%20Layer%20cd56d4399f544746b56994ccba2cc09e/identifier2.jpeg)

# Host

네트워크에 연결된 컴퓨터를 Host라고 한다. **IP Address로 식별한다.**

![host.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/OSI%207%20Layer%20cd56d4399f544746b56994ccba2cc09e/host.jpeg)

# Switch

![switch1.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/OSI%207%20Layer%20cd56d4399f544746b56994ccba2cc09e/switch1.jpeg)

![switch2.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/OSI%207%20Layer%20cd56d4399f544746b56994ccba2cc09e/switch2.jpeg)

# Problem Solving (Algorithm & SQL)

### BOJ 14497 **주난의 난(難)**

[](https://www.acmicpc.net/problem/14497)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    
    private static int n = 0;
    private static int m = 0;
    private static int sy = 0;
    private static int sx = 0;
    private static int ey = 0;
    private static int ex = 0;
    
    private static char[][] map;
    private static int[][] dist;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());
        
        tokens = new StringTokenizer(br.readLine());
        sy = Integer.parseInt(tokens.nextToken()) - 1;
        sx = Integer.parseInt(tokens.nextToken()) - 1;
        ey = Integer.parseInt(tokens.nextToken()) - 1;
        ex = Integer.parseInt(tokens.nextToken()) - 1;
        
        map = new char[n][m];
        dist = new int[n][m];
        for(int i = 0; i < n; i++)
            Arrays.fill(dist[i], Integer.MAX_VALUE);
        
        for(int i = 0; i < n; i++)
            map[i] = br.readLine().toCharArray();
        
        System.out.println(dijkstra());
    }   //   main-end
    
    private static int dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        dist[sy][sx] = 0;
        pq.offer(new Node(sy, sx, dist[sy][sx]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cy = cur.y;
            int cx = cur.x;
            int ccost = cur.cost;
            
            if(dist[cy][cx] < ccost)
                continue;
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                
                if(isIn(ny, nx)) {
                    int ncost = map[ny][nx] == '0' ? ccost : ccost + 1;
                    
                    if(dist[ny][nx] > ncost) {
                        dist[ny][nx] = ncost;
                        pq.offer(new Node(ny, nx, dist[ny][nx]));
                    }
                }
            }
        }
        
        return dist[ey][ex];
    }
    
    private static int[] dy = {1, 0, -1, 0};
    private static int[] dx = {0, 1, 0, -1};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < n) && (0 <= x && x < m);
    }
    
    private static class Node implements Comparable<Node> {
        int y;
        int x;
        int cost;
        
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
}   //   Main-class-end
```