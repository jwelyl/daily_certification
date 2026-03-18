# 26_03_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2021 최소 환승 경로

[2021번: 최소 환승 경로](http://boj.ma/2021/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
  private static final int INF = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //    역 개수
    int l = Integer.parseInt(st.nextToken()); //    노선 개수

    int src;               //    출발역
    int dst;               //    도착역

    List<Integer>[] stationToLine = new ArrayList[n + 1];
    List<Integer>[] lineToStation = new ArrayList[l + 1];
    int[] dist = new int[n + 1];                            //    checked[l] : l번 노선 체크했으면 true
    boolean[] checked = new boolean[l + 1];                 //    dist[s] : s번 역에 닿기 위한 최소 환승 개수

    for(int station = 1; station <= n; station++) {
      stationToLine[station] = new ArrayList<>();
      dist[station] = INF;
    }

    for(int line = 1; line <= l; line++) {
      lineToStation[line] = new ArrayList<>();

      st = new StringTokenizer(br.readLine());
      while(st.hasMoreTokens()) {
        int station = Integer.parseInt(st.nextToken());
        if(station != -1) {
          stationToLine[station].add(line);
          lineToStation[line].add(station);
        }
      }
    }

    st = new StringTokenizer(br.readLine());
    src = Integer.parseInt(st.nextToken());
    dst = Integer.parseInt(st.nextToken());

    System.out.println(bfs(src, dst, stationToLine, lineToStation, dist, checked));
  }    //    main-end

  private static int bfs(int src, int dst, List<Integer>[] stationToLine, List<Integer>[] lineToStation, int[] dist, boolean[] checked) {
    Queue<int[]> queue = new ArrayDeque<>();
    int time = 0;

    dist[src] = 0;
    queue.offer(new int[] {src, dist[src]});

    while(!queue.isEmpty()) {
      int size = queue.size();

      for(int i = 0; i < size; i++) {
        int[] cur = queue.poll();
        int curStation = cur[0];

        for(int curLine : stationToLine[curStation]) {
          if(!checked[curLine]) {    //    아직 체크 안한 호선의 경우
            checked[curLine] = true;

            for(int nextStation : lineToStation[curLine]) {    //    현재 호선에서 갈 수 있는 역들
              if(time < dist[nextStation]) {
                dist[nextStation] = time;
                queue.offer(new int[] {nextStation, dist[nextStation]});
              }
            }
          }
        }
      }

      time++;
    }

    return dist[dst] == INF ? -1 : dist[dst];
  }
}    //    Main-class-end
```