# 26_03_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2637 장난감 조립

[http://boj.ma/2637/t](http://boj.ma/2637/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Queue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int n = Integer.parseInt(br.readLine());
    int m = Integer.parseInt(br.readLine());

    List<int[]>[] graph = new ArrayList[n + 1];   //	Node가 [subpart 이름, subpart 개수]인 그래프
    Set<Integer> base = new HashSet<>();	      //	기본 부품 집합
    int[] indegrees = new int[n + 1];
    int[][] dp = new int[n + 1][n + 1];           //	dp[partNum][subpartNum] : partNum을 1개 만드는데 필요한 subpartNum

    for(int v = 0; v <= n; v++)
      graph[v] = new ArrayList<>();
    for(int v = 1; v <= n; v++)
      base.add(v);

    for(int e  = 0; e < m; e++) {
      int partName;
      int subpart;
      int numOfSubpart;
      int[] node;

      StringTokenizer st = new StringTokenizer(br.readLine());

      partName = Integer.parseInt(st.nextToken());
      subpart = Integer.parseInt(st.nextToken());
      numOfSubpart = Integer.parseInt(st.nextToken());

      base.remove(partName);	//	partName은 기본 부품이 아니므로 제거

      //	partName을 만드는데 subpart가 numOfSubpart개 필요함
      node = new int[] {partName, numOfSubpart};
      graph[subpart].add(node);
      indegrees[partName]++;
    }

    topologicalSorting(n, graph, indegrees, dp, base);
    for(int v = 1; v <= n; v++) {
      if(base.contains(v))
        sb.append(v).append(" ").append(dp[n][v]).append("\n");
    }

    System.out.println(sb);
  } //  main-end

  private static void topologicalSorting(int n, List<int[]>[] graph, int[] indegrees, int[][] dp, Set<Integer> base) {
    Queue<Integer> queue = new ArrayDeque<>();
    for(int i = 1; i <= n; i++) {
      if(indegrees[i] == 0)
        queue.offer(i);
    }

    while(!queue.isEmpty()) {
      int subpart = queue.poll();

      for(int[] node :  graph[subpart]) {
        int partNum = node[0];		//	subpart로 만들 part 번호
        int subpartCnt = node[1];	//	subpart로 partNum 만드는데 필요한 개수
        indegrees[partNum]--;

        if(base.contains(subpart)) 	//	subpart가 기본 부품일 경우
          dp[partNum][subpart] += subpartCnt;	//	partNum을 만드는데 subpart가 subpartCnt개 필요함
        else {	//	subpart가 기본 부품이 아닐 경우
          for(int basePart : base) {
            //	subpart를 만드는데 basePart가 dp[subpart][basePart]개 필요함
            //	그런 subpart가 partNum을 만드는데 subpartCnt개 필요함
            //	따라서 partNum을 만드는데에는 basePart가 dp[subpart][basePart] * subpartCnt개 만큼 추가로 필요함
            dp[partNum][basePart] += dp[subpart][basePart] * subpartCnt;
          }
        }

        if(indegrees[partNum] == 0)
          queue.offer(partNum);
      }
    }
  }
} //  Main-class-end
```