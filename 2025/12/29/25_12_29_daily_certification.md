# 25_12_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1810 징검다리 달리기 2

[1810번: 징검다리 달리기 2](http://boj.ma/1810/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static final double INF = Double.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //  징검다리 개수
    int f = Integer.parseInt(st.nextToken()); //  결승선 y 좌표

    int[][] posArr = new int[n + 1][2];
    //  xPosList[y] : y 좌표가 y인 징검다리의 x 좌표 리스트 (정렬 상태 유지)
    List<Pos>[] xPosList = new ArrayList[f + 1];

    List<Edge>[] graph = new ArrayList[n + 1];
    for (int y = 0; y <= f; y++)
      xPosList[y] = new ArrayList<>();

    for (int num = 0; num <= n; num++)
      graph[num] = new ArrayList<>();

    xPosList[0].add(new Pos(0, 0));

    for (int num = 1; num <= n; num++) {
      st = new StringTokenizer(br.readLine());
      int x = Integer.parseInt(st.nextToken());
      int y = Integer.parseInt(st.nextToken());

      posArr[num][0] = x;
      posArr[num][1] = y;
      xPosList[y].add(new Pos(num, x));
    }

    for (int y = 0; y <= f; y++)
      Collections.sort(xPosList[y]);

    makeGraph(n, f, posArr, xPosList, graph);

    System.out.println(dijkstra(n, f, posArr, graph));
  } //	main-end

  private static int dijkstra(int n, int f, int[][] posArr, List<Edge>[] graph) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    double[] dist = new double[n + 1];
    double answer = INF;

    Arrays.fill(dist, INF);
    pq.offer(new Node(0, 0));

    while(!pq.isEmpty()) {
      Node cur = pq.poll();
      int cnum = cur.num;
      double ccost = cur.cost;

      if(dist[cnum] < ccost)
        continue;
      for(Edge next : graph[cnum]) {
        int nnum = next.num;
        double ncost = ccost + next.weight;

        if(ncost < dist[nnum]) {
          dist[nnum] = ncost;
          pq.offer(new Node(nnum, dist[nnum]));
        }
      }
    }

    for(int num = 1; num <= n; num++) {
      if (posArr[num][1] == f)
        answer = Math.min(answer, dist[num]);
    }

    return answer == INF ? -1 : (int)Math.round(answer);
  }

  private static void makeGraph(int n, int f, int[][] posArr, List<Pos>[] xPosList, List<Edge>[] graph) {
    for (int num = 0; num <= n; num++) {
      int[] pos = posArr[num];
      int x = pos[0];
      int y = pos[1];

      for (int yy = Math.max(0, y - 2); yy <= Math.min(f, y + 2); yy++) {
        List<Pos> posList = xPosList[yy];

        //  posList에 저장된 pos들 중 x 좌표가 x - 2 이상인 pos 중 가장 왼쪽에 존재하는 pos의 idx
        int idx1 = binarySearch(x - 2, posList, true);
        //  posList에 저장된 pos들 중 x 좌표가 x + 2 이하인 pos 중 가장 오른쪽
        int idx2 = binarySearch(x + 2, posList, false);

        if(idx1 == -1 || idx2 == -1)
          continue;

        for (int i = idx1; i <= idx2; i++) {
          Pos npos = posList.get(i);
          int nnum = npos.num;

          if (num == nnum)
            continue;

          double weight = weight(x, y, posArr[nnum][0], posArr[nnum][1]);
          graph[num].add(new Edge(nnum, weight));
        }
      }
    }
  }

  private static double weight(int x1, int y1, int x2, int y2) {
    return Math.sqrt(((long) x1 - x2) * (x1 - x2) + ((long) y1 - y2) * (y1 - y2));
  }

  private static int binarySearch(int target, List<Pos> posList, boolean lowerBound) {
    int start = 0;
    int end = posList.size() - 1;
    int res = -1;

    while (start <= end) {
      int mid = (start + end) / 2;
      int x = posList.get(mid).x;

      if(lowerBound) {    //  target 이상인 것중 가장 왼쪽에 있는 것을 찾는 경우
        if(x >= target) {
          res = mid;
          end = mid - 1;
        }
        else
          start = mid + 1;
      }
      else {  //  target 이하인 것중 가장 오른쪽에 있는 것을 찾는 경우
        if(x <= target) {
          res = mid;
          start = mid + 1;
        }
        else
          end = mid - 1;
      }
    }

    return res;
  }

  private static class Pos implements Comparable<Pos> {
    public final int num; //  징검다리 번호
    public final int x;   //  해당 징검다리 x 좌표

    public Pos(int num, int x) {
      this.num = num;
      this.x = x;
    }

    @Override
    public int compareTo(Pos other) {
      return Integer.compare(this.x, other.x);
    }
  }

  private static class Edge {
    public final int num;
    public final double weight;

    public Edge(int num, double weight) {
      this.num = num;
      this.weight = weight;
    }
  }

  private static class Node implements Comparable<Node> {
    public final int num;
    public final double cost;

    public Node(int num, double cost) {
      this.num = num;
      this.cost = cost;
    }

    @Override
    public int compareTo(Node other) {
      return Double.compare(this.cost, other.cost);
    }
  }
} //	Main-class-end
```