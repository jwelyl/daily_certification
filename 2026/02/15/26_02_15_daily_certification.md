# 26_02_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1432 그래프 수정

[1432번: 그래프 수정](http://boj.ma/1432/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.PriorityQueue;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());  //  정점 개수
    List<Integer>[] reverseGraph = new ArrayList[n + 1];
    int[] outDegrees = new int[n + 1];
    for (int v = 0; v <= n; v++)
      reverseGraph[v] = new ArrayList<>();

    for(int v1 = 1; v1 <= n; v1++) {
      char[] adjMatrix = br.readLine().toCharArray();

      for(int v2 = 1; v2 <= n; v2++) {
        if(adjMatrix[v2 - 1] == '1') {
          reverseGraph[v2].add(v1);
          outDegrees[v1]++;
        }
      }
    }

    topologicalSorting(n, reverseGraph, outDegrees);
  }    //	main-end

  private static void topologicalSorting(int n, List<Integer>[] reverseGraph, int[] outDegrees) {
    PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
    int order = n;
    int[] topologicalOrder = new int[n + 1];
    StringBuilder sb = new StringBuilder();

    for(int v = 1; v <= n; v++)
      if(outDegrees[v] == 0)
        pq.offer(v);

    while(!pq.isEmpty()) {
      int cur = pq.poll();
      topologicalOrder[cur] = order--;

      for(int next : reverseGraph[cur]) {
        outDegrees[next]--;
        if(outDegrees[next] == 0)
          pq.offer(next);
      }
    }

    if (order != 0)  //  cycle 발생
      System.out.println(-1);
    else {
      for(int v = 1; v <= n; v++)
        sb.append(topologicalOrder[v]).append(" ");
      System.out.println(sb);
    }
  }
}    //	Main-class-end
```