# 26_01_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23632 쿠키런 킹덤

[23632번: 쿠키런 킹덤](http://boj.ma/23632/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //  자원 가짓수
    int m = Integer.parseInt(st.nextToken()); //  이미 지어진 건물 개수
    int t = Integer.parseInt(st.nextToken()); //  제한시간

    //  1 ~ n : 건물 번호
    //  n + 1 ~ 2 * n : 자원 번호 + n
    List<Edge>[] graph = new ArrayList[2 * n + 1];
    int[] indegrees = new int[2 * n + 1];
    Queue<Integer> queue = new ArrayDeque<>();
    boolean[] visited = new boolean[2 * n + 1];
    //  각 자원 또는 건물이 마련되는 최소 시간
    int[] time = new int[2 * n + 1];

    //  제한시간 t 동안 지을 수 있는 건물 개수
    int answer = 0;

    for(int v = 0; v <= 2 * n; v++) {
      graph[v] = new ArrayList<>();
      time[v] = Integer.MAX_VALUE;
    }

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < m; i++) {
      int v = Integer.parseInt(st.nextToken()); //  최초에 지어진 건물
      time[v] = 0;
      visited[v] = true;
      queue.offer(v);
    }

    for(int v = 1; v <= n; v++) {
      st = new StringTokenizer(br.readLine());
      int cnt = Integer.parseInt(st.nextToken()); //  건물 v가 생산해낼 수 있는 자원 개수

      for(int i = 0; i < cnt; i++) {
        int r = Integer.parseInt(st.nextToken()) + n;
        graph[v].add(new Edge(r, 0));
      }
    }

    for(int i = 0; i < n - m; i++) {  //  아직 지어지지 않은 건물
      st = new StringTokenizer(br.readLine());
      int v = Integer.parseInt(st.nextToken());     //  아직 지어지지 않은 건물 번호
      int cnt = Integer.parseInt(st.nextToken());   //  그 건물이 필요로 하는 자원 수

      for(int j = 0; j < cnt; j++) {
        int r = Integer.parseInt(st.nextToken()) + n;
        indegrees[v]++;
        graph[r].add(new Edge(v, 1));
      }
    }

    topologicalSort(n, graph, queue, indegrees, visited, time);

    for(int v = 1; v <= n; v++) {
      if (time[v] <= t) {
        answer++;
        sb.append(v).append(" ");
      }
    }

    System.out.println(answer);
    System.out.println(sb);
  } //  main-end

  private static void topologicalSort(int n, List<Edge>[] graph, Queue<Integer> queue, int[] indegrees, boolean[] visited, int[] time) {
    while(!queue.isEmpty()) {
      int cur = queue.poll();

      for(Edge edge : graph[cur]) {
        int next = edge.vertex;
        indegrees[next]--;

        if(indegrees[next] <= 0 && !visited[next]) {
          visited[next] = true;
          time[next] = time[cur] + edge.cost;
          queue.offer(next);
        }
      }
    }
  }

  private static class Edge {
    public int vertex;
    public int cost;

    public Edge(int vertex, int cost) {
      this.vertex = vertex;
      this.cost = cost;
    }
  }
} //  Main-class-end
```