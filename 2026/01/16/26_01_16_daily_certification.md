# 26_01_16_daily_certification

```
[#016 koreii] 데일리인증 20260116
1. CS
- 도메인 학습
- 데이터베이스 테이블 설계
2. 알고리즘 (복습) 문제 풀이
- Dijkstra (BOJ 20160)
```

# Problem Solving (Algorithm & SQL)

### BOJ 20160 **야쿠르트 아줌마 야쿠르트 주세요**

[20160번: 야쿠르트 아줌마 야쿠르트 주세요](http://boj.ma/20160/t)

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
  private static final int NONE = Integer.MAX_VALUE;
  private static final long INF = 100_000L * 100_000L * 10 + 1;

  private static final int[] YOGURT = new int[10];

  private static int answer = NONE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    //	정점 개수
    int n = Integer.parseInt(st.nextToken());
    //	간선 개수
    int m = Integer.parseInt(st.nextToken());

    List<Node>[] graph = new ArrayList[n + 1];
    long[] yogurtDist = new long[n + 1];
    long[] myDist = new long[n + 1];
    for(int v = 0; v <= n; v++)
      graph[v] = new ArrayList<>();

    for(int e = 0; e < m; e++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      int c = Integer.parseInt(st.nextToken());

      graph[v1].add(new Node(v2, c));
      graph[v2].add(new Node(v1, c));
    }

    st = new StringTokenizer(br.readLine());
    for(int v = 0; v < 10; v++)
      YOGURT[v] = Integer.parseInt(st.nextToken());

    int myStart = Integer.parseInt(br.readLine());
    dijkstra(myStart, 0, graph, myDist);

    int cur = YOGURT[0];	//	현재 요구르트 위치
    long time = 0;			//	그때의 시간

    if(myDist[cur] <= time) {
      if(cur < answer)
        answer = cur;
    }

    int idx = 1;
    while(idx < 10) {
      int next = YOGURT[idx];	//	다음 요구르트 위치

      dijkstra(cur, time, graph, yogurtDist);

      if(yogurtDist[next] == INF) {	//	다음 요구르트 위치로 갈 수 없으면
        idx++;
        continue;
      }

      time = yogurtDist[next];
      cur = next;
      idx++;

      if(myDist[cur] <= time) {
        if(cur < answer)
          answer = cur;
      }
    }

    System.out.println(answer == NONE ? -1 : answer);
  }	//	main-end

  private static class Node implements Comparable<Node> {
    int vertex;
    long cost;

    public Node(int vertex, long cost) {
      this.vertex = vertex;
      this.cost = cost;
    }

    public int compareTo(Node other) {
      return Long.compare(this.cost, other.cost);
    }
  }

  private static void dijkstra(int start, long startTime, List<Node>[] graph, long[] dist) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    Arrays.fill(dist, INF);
    dist[start] = startTime;
    pq.offer(new Node(start, dist[start]));

    while(!pq.isEmpty()) {
      Node cur = pq.poll();
      int cv = cur.vertex;
      long cc = cur.cost;

      if(dist[cv] < cc)
        continue;

      for(Node next : graph[cv]) {
        int nv = next.vertex;
        long nc = cc + next.cost;

        if(nc < dist[nv]) {
          dist[nv] = nc;
          pq.offer(new Node(nv, dist[nv]));
        }
      }
    }
  }
}	//	Main-class-end
```