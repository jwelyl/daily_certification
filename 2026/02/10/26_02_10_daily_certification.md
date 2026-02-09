# 26_02_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20313 출퇴근

[20313번: 출퇴근](http://boj.ma/20313/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());   //  건물 개수
    int m = Integer.parseInt(st.nextToken());   //  도로 개수
    int src = Integer.parseInt(st.nextToken()); //  출발 건물
    int dst = Integer.parseInt(st.nextToken()); //  도착 건물

    int k;
    //  edgeInfoes[e] : {연결 정점 1, 연결 정점 2, 간선 비용}
    int[][] edgeInfos = new int[m + 1][3];
    int[][] costMap;

    //  graph[v] : v와 연결된 간선 번호
    List<Integer>[] graph = new ArrayList[n + 1];
    for(int v = 0; v <= n; v++)
      graph[v] = new ArrayList<>();

    for(int e = 1; e <= m; e++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int cost = Integer.parseInt(st.nextToken());
      edgeInfos[e][0] = v1;
      edgeInfos[e][1] = v2;
      edgeInfos[e][2] = cost;

      graph[v1].add(e);
      graph[v2].add(e);
    }

    k = Integer.parseInt(br.readLine());
    //  costMap[k][e] : e번째 도로가 k번 변경되었을때의 도로 이동 시간
    costMap = new int[k + 1][m + 1];

    for(int e = 1; e <= m; e++)
      costMap[0][e] = edgeInfos[e][2];  //  0번 변경되었을때의 도로 이동 시간

    for(int i = 1; i <= k; i++) {
      st = new StringTokenizer(br.readLine());
      for(int e = 1; e <= m; e++)
        costMap[i][e] = Integer.parseInt(st.nextToken());
    }

    System.out.println(dijkstra(n, k, src, dst, graph, edgeInfos, costMap));
  }    //    main-end

  private static long dijkstra(int n, int k, int src, int dst, List<Integer>[] graph, int[][] edgeInfos, int[][] costMap) {
    PriorityQueue<Node> pq = new PriorityQueue<>();

    //  dist[change][v] : 도로 변경을 change번 한 상태로 v에 도달하는 최소 시간
    long[][] dist = new long[k + 1][n + 1];
    long res = Long.MAX_VALUE;

    for(int i = 0; i <= k; i++)
      Arrays.fill(dist[i], Long.MAX_VALUE);

    dist[0][src] = 0;
    pq.offer(new Node(src, 0, dist[0][src]));

    while(!pq.isEmpty()) {
      Node cur = pq.poll();
      int cv = cur.vertex;
      int cchange = cur.change;
      long cc = cur.cost;

      if(dist[cchange][cv] < cc)
        continue;

      //  1. 현재 변경 상태로 이동한다고 가정
      for(int e : graph[cv]) {
        int[] edge = edgeInfos[e];
        int nv = edge[0] == cv ? edge[1] : edge[0]; //  다음에 갈 정점
        long nc = cc + costMap[cchange][e];         //  현재 변경 상태로 다음 정점 도달 비용

        if(dist[cchange][nv] > nc) {
          dist[cchange][nv] = nc;
          pq.offer(new Node(nv, cchange, dist[cchange][nv]));
        }
      }
      //  2. 한번 더 변경
      if(cchange < k) {
        if(dist[cchange + 1][cv] > cc) {
          dist[cchange + 1][cv] = cc;
          pq.offer(new Node(cv, cchange + 1, dist[cchange + 1][cv]));
        }
      }
    }

    for(int i = 0; i <= k; i++)
      res = Math.min(res, dist[i][dst]);

    return res;
  }

  private static class Node implements Comparable<Node> {
    public int vertex;
    public int change;   //  도로 비용 바꾼 횟수
    public long cost;    //  도로 비용 바꾼 횟수가 change인 상태로 vertex까지 오는 최소 시간

    public Node(int vertex, int change, long cost) {
      this.vertex = vertex;
      this.change = change;
      this.cost = cost;
    }

    @Override
    public int compareTo(Node other) {
      return Long.compare(this.cost, other.cost);
    }
  }
}    //    Main-class-end
```