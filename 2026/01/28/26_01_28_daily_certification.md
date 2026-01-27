# 26_01_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1516 게임 개발

[1516번: 게임 개발](http://boj.ma/1516/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int n = Integer.parseInt(bf.readLine());

    List<Integer>[] graph = new ArrayList[n + 1];
    int[] indegrees = new int[n + 1];
    int[] buildTimes = new int[n + 1];
    int[] completeTimes = new int[n + 1];

    for(int v = 0; v <= n; v++)
      graph[v] = new ArrayList<>();

    Arrays.fill(completeTimes, Integer.MIN_VALUE);

    for(int num = 1; num <= n; num++) {
      StringTokenizer st = new StringTokenizer(bf.readLine());

      buildTimes[num] = Integer.parseInt(st.nextToken());

      while(st.hasMoreTokens()) {
        int former = Integer.parseInt(st.nextToken());

        if(former != -1) {
          graph[former].add(num);
          indegrees[num]++;
        }
      }
    }

    topologicalSort(n, graph, indegrees, buildTimes, completeTimes);
    for(int v = 1; v <= n; v++)
      sb.append(completeTimes[v]).append("\n");

    System.out.print(sb);
  }	//	main-end

  private static void topologicalSort(int n, List<Integer>[] graph, int[] indegrees, int[] buildTimes, int[] completeTimes) {
    Queue<Integer> queue = new ArrayDeque<>();
    for(int i = 1; i <= n; i++) {
      if(indegrees[i] == 0) {
        queue.offer(i);
        completeTimes[i] = buildTimes[i];
      }
    }

    while(!queue.isEmpty()) {
      int cur = queue.poll();

      for(int next :  graph[cur]) {
        indegrees[next]--;
        completeTimes[next] = Integer.max(completeTimes[next], completeTimes[cur] + buildTimes[next]);

        if(indegrees[next] == 0)
          queue.offer(next);
      }
    }
  }
} //  Main-class-end
```