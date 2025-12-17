# 25_12_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 34560 브레인롯 챔피언십

[34560번: 브레인롯 챔피언십](http://boj.ma/34560/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    List<Integer>[] graph = new ArrayList[n + 1];
    int[] indegrees = new int[n + 1];
    Brainrot[] brainrots = new Brainrot[n + 1];
    for(int v = 1; v <=  n; v++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      String name = st.nextToken();
      int potenza = Integer.parseInt(st.nextToken());
      int agilita = Integer.parseInt(st.nextToken());
      int surrealismo = Integer.parseInt(st.nextToken());
      brainrots[v] = new Brainrot(name, potenza, agilita, surrealismo);
      graph[v] = new ArrayList<>();
    }

    for(int v1 = 1; v1 < n; v1++) {
      for(int v2 = v1 + 1; v2 <= n; v2++) {
        int win1 = 0;
        int win2 = 0;

        if(brainrots[v1].potenza > brainrots[v2].potenza)
          win1++;
        else if(brainrots[v1].potenza == brainrots[v2].potenza) {
          win1++;
          win2++;
        }
        else
          win2++;
        if(brainrots[v1].agilita > brainrots[v2].agilita)
          win1++;
        else if(brainrots[v1].agilita == brainrots[v2].agilita) {
          win1++;
          win2++;
        }
        else
          win2++;
        if(brainrots[v1].surrealismo > brainrots[v2].surrealismo)
          win1++;
        else if(brainrots[v1].surrealismo == brainrots[v2].surrealismo) {
          win1++;
          win2++;
        }
        else
          win2++;

        if(win1 > win2) {
          graph[v1].add(v2);
          indegrees[v2]++;
        }
        else if(win1 < win2) {
          graph[v2].add(v1);
          indegrees[v1]++;
        }
      }
    }

    topologicalSort(n, brainrots, graph, indegrees);
  } //  main-end

  private static void topologicalSort(int n, Brainrot[] brainrots, List<Integer>[] graph, int[] indegrees) {
    Queue<Integer> queue = new ArrayDeque<>();
    PriorityQueue<String> pq = new PriorityQueue<>();
    StringBuilder sb = new StringBuilder();
    int sorted = 0;

    for(int v = 1; v <= n; v++) {
      if(indegrees[v] == 0) {
        queue.offer(v);
        pq.offer(brainrots[v].name);
        sorted++;
      }
    }

    while(!queue.isEmpty()) {
      int v = queue.poll();

      for(int nv : graph[v]) {
        indegrees[nv]--;
        if(indegrees[nv] == 0) {
          queue.offer(nv);
          sorted++;
        }
      }
    }

    if(sorted != n)
      sb.append("Paradoxe Absurdo\n");
    else {
      while(!pq.isEmpty())
        sb.append(pq.poll()).append("\n");
    }

    System.out.print(sb);
  }

  private static class Brainrot {
    public final String name;
    public final int potenza;
    public final int agilita;
    public final int surrealismo;

    public Brainrot(String name, int potenza, int agilita, int surrealismo) {
      this.name = name;
      this.potenza = potenza;
      this.agilita = agilita;
      this.surrealismo = surrealismo;
    }
  }
} //  Main-class-end
```