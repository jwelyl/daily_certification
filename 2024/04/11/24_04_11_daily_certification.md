# 24_04_11_daily_certification

# 데이터베이스

## Transaction

### write-lock

### read-lock

### Two-Phase Locking Protocol

# Problem Solving (Algorithm & SQL)

**BOJ 1277 발전소 설치**

[1277번: 발전소 설치](https://www.acmicpc.net/problem/1277)

![ex1.jpeg](24_04_11_daily_certification%203e17c63258564048a3003db39937122d/ex1.jpeg)

예제 1의 발전소 위치는 위와 같다. 발전소는 최대 1,000개이므로, 발전소 사이에 존재 가능한 전선 수는 최대 1,000,000개이다. 이 중 일부는 이미 주어져있다.

![ex1_1.jpeg](24_04_11_daily_certification%203e17c63258564048a3003db39937122d/ex1_1.jpeg)

이미 주어진 전선의 길이는 0으로 설정하면, n과 연결되기 위한 최소 비용을 계산할 때 추가되는 전선의 최소 비용만 고려하면 된다.

n개의 발전소 중 서로 다른 두 발전소 쌍을 모두 골라서 두 발전소 사이의 모든 거리를 구해준다. 이 때 거리가 m을 초과하는 경우가 아니면 해당 거리의 전선을 추가하여 그래프를 만든다. 그래프를 만든 후에는 Dijkstra Algorithm으로 최단거리를 구하면 된다.

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    
    private static int n;        //    발전소 개수 (1 ~ n)
    private static double m;     //    임의의 두 발전소를 직접 이을 수 있는 최대 전선 길이
    private static int w;        //    일단 연결된 두 발전소 개수
    
    private static boolean[][] connected;    //   두 발전소 i, j가 이미 연결되어 있으면 connected[i][j] = true
    
    private static Pos[] posArr;          //    각 발전소 좌표 배열
    private static List<Node>[] graph;    //    그래프
    
    private static double[] dist;    //    dist[i] : 1부터 i까지 연결하는데 필요한 최소 전선 길이
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        
        n = Integer.parseInt(tokens.nextToken());
        w = Integer.parseInt(tokens.nextToken());
        
        m = Double.parseDouble(br.readLine());
        
        connected = new boolean[n + 1][n + 1];
        posArr = new Pos[n + 1];
        
        graph = new ArrayList[n + 1];
        for(int i = 1; i <= n; i++) {
            graph[i] = new ArrayList<>();
        }
        
        //    각 발전소 좌표 입력
        for(int i = 1; i <= n; i++) {
            tokens = new StringTokenizer(br.readLine());
            
            posArr[i] = new Pos(Integer.parseInt(tokens.nextToken()), Integer.parseInt(tokens.nextToken()));
        }
        
        //    이미 연결된 발전소 쌍 입력
        for(int i = 0; i < w; i++) {
            tokens = new StringTokenizer(br.readLine());
            connected[Integer.parseInt(tokens.nextToken())][Integer.parseInt(tokens.nextToken())] = true;
        }
        
        for(int i = 1; i <= n - 1; i++) {
            for(int j = i + 1; j <= n; j++) {
                if(connected[i][j] || connected[j][i]) {    //    이미 두 발전소가 연결되어 있다면
                    graph[i].add(new Node(j, 0.0));
                    graph[j].add(new Node(i, 0.0));    //    두 발전소 i, j 사이 거리를 0으로 설정
                } else {    //    두 발전소를 직접 잇는 직선 거리 구하기
                    int x1 = posArr[i].x;
                    int y1 = posArr[i].y;
                    int x2 = posArr[j].x;
                    int y2 = posArr[j].y;
                    
                    double distance = distance(x1, y1, x2, y2);
                    
                    if(distance <= m) {
                        graph[i].add(new Node(j, distance));
                        graph[j].add(new Node(i, distance));
                    }
                }
            }
        }
        
        dijkstra();
        
        System.out.println((int)(dist[n] * 1000));
    }   //  main-end
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        dist = new double[n + 1];
        Arrays.fill(dist, Double.MAX_VALUE);
        dist[1] = 0;
        
        pq.offer(new Node(1, dist[1]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            double cdist = cur.dist;
            
            if(cdist > dist[cv])    //    1에서부터 cv까지 오는 거리가 이미 알려진 최단거리보다 클 경우
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                double ndist = cdist + next.dist;    //    1에서부터 cv를 거쳐서 nv까지 오는 거리
                
                if(ndist < dist[nv]) {    //    cv를 거쳐서 nv로 오는게 더 짧을 경우
                    dist[nv] = ndist;
                    pq.offer(new Node(nv, ndist));
                }
            }
        }   //  while-end
    }   //  dijkstra-end
    
    private static double distance(int x1, int y1, int x2, int y2) {
        return Math.sqrt((long)(x1 - x2) * (x1 - x2) + (long)(y1 - y2) * (y1 - y2));
    }
    
    private static class Pos {
        int x;
        int y;
        
        public Pos(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
    
    private static class Node implements Comparable<Node> {
        int vertex;
        double dist;
        
        public Node(int vertex, double dist) {
            this.vertex = vertex;
            this.dist = dist;
        }
        
        @Override
        public int compareTo(Node node) {
            return Double.compare(this.dist, node.dist);
        }
    }
}   //  Main-class-end
```