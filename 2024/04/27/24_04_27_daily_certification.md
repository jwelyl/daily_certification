# 24_04_27_daily_certification

```
[koreii] #118 데일리인증
20240427
알고리즘 & 코딩 테스트 대비
- 코드트리 위상 정렬 + DP 문제 풀이
```

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 친구의 키**

[https://www.codetree.ai/missions/9/problems/height-of-friends/description](https://www.codetree.ai/missions/9/problems/height-of-friends/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/height-of-friends/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  친구 수
    private static int m;   //  키 비교 횟수

    private static List<Integer>[] graph;   //  키 순서 비교 그래프
    private static int[] indegrees;         //  진입 차수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        graph = new ArrayList[n + 1];
        for(int i = 0; i <= n; i++)
            graph[i] = new ArrayList<>();

        indegrees = new int[n + 1];

        for(int i = 0; i < m; i++) {
            tokens = new StringTokenizer(br.readLine());

            int a = Integer.parseInt(tokens.nextToken());
            int b = Integer.parseInt(tokens.nextToken());

            graph[a].add(b);
            indegrees[b]++;
        }

        topologicalSort();
    }   //  main-end

    private static void topologicalSort() {
        Queue<Integer> q = new LinkedList<>();
        for(int i = 1; i <= n; i++) {
            if(indegrees[i] == 0) {
                q.offer(i);
                sb.append(i).append(" ");
            }
        }

        while(!q.isEmpty()) {
            int cur = q.poll();

            for(int next : graph[cur]) {
                indegrees[next]--;

                if(indegrees[next] == 0) {
                    q.offer(next);
                    sb.append(next).append(" ");
                }
            }
        }

        System.out.print(sb);
    }
}   //  Main-class-end
```

**CO{)E TREE 친구의 키 2**

[https://www.codetree.ai/missions/9/problems/height-of-friends-2/description](https://www.codetree.ai/missions/9/problems/height-of-friends-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/height-of-friends-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  친구 수
    private static int m;   //  키 비교 횟수

    private static List<Integer>[] graph;   //  키 순서 비교 그래프
    private static int[] indegrees;         //  진입 차수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        graph = new ArrayList[n + 1];
        for(int i = 0; i <= n; i++)
            graph[i] = new ArrayList<>();

        indegrees = new int[n + 1];

        for(int i = 0; i < m; i++) {
            tokens = new StringTokenizer(br.readLine());

            int a = Integer.parseInt(tokens.nextToken());
            int b = Integer.parseInt(tokens.nextToken());

            graph[a].add(b);
            indegrees[b]++;
        }

        topologicalSort();
    }   //  main-end

    private static void topologicalSort() {
        Queue<Integer> q = new LinkedList<>();
        int cnt = 0;
        
        for(int i = 1; i <= n; i++) {
            if(indegrees[i] == 0) {
                q.offer(i);
                cnt++;
            }
        }

        while(!q.isEmpty()) {
            int cur = q.poll();

            for(int next : graph[cur]) {
                indegrees[next]--;

                if(indegrees[next] == 0) {
                    q.offer(next);
                    cnt++;
                }
            }
        }

        System.out.println(cnt == n ? "Consistent" : "Inconsistent");
    }
}   //  Main-class-end
```

**CO{)E TREE 선행 작업**

[https://www.codetree.ai/missions/9/problems/predecessor/description](https://www.codetree.ai/missions/9/problems/predecessor/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/predecessor/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  친구 수

    private static List<Integer>[] graph;   //  키 순서 비교 그래프
    private static int[] indegrees;         //  진입 차수
    private static int[] times;             //  times[i] : i번째 작업을 끝내는 데 걸리는 시간
    private static int[] endTimes;          //  endTimes[i] : i번째 작업들의 선행 작업을 모두 끝내고 i번째 작업을 끝내는데 걸리는 시간
    private static int maxTime = 0;         //  가장 늦게 끝나는 작업이 끝나는 시간

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        graph = new ArrayList[n + 1];
        for(int i = 0; i <= n; i++)
            graph[i] = new ArrayList<>();

        indegrees = new int[n + 1];
        times = new int[n + 1];
        endTimes = new int[n + 1];

        for(int i = 1; i <= n; i++) {
            tokens = new StringTokenizer(br.readLine());

            int time = Integer.parseInt(tokens.nextToken());    //  i번째 작업에 걸리는 시간
            times[i] = time;

            int prevCnt = Integer.parseInt(tokens.nextToken()); //  i번째 작업의 선행 작업 개수
            for(int j = 0; j < prevCnt; j++) {
                int prev = Integer.parseInt(tokens.nextToken());    //  i번째 작업의 선행 작업 prev

                graph[prev].add(i);
                indegrees[i]++;
            }
        }

        topologicalSort();
    }   //  main-end

    private static void topologicalSort() {
        Queue<Integer> q = new LinkedList<>();
        
        for(int i = 1; i <= n; i++) {
            if(indegrees[i] == 0) { //  선행 작업이 없는 작업들
                q.offer(i);
                endTimes[i] = times[i];
                maxTime = Math.max(maxTime, endTimes[i]);
            }
        }

        while(!q.isEmpty()) {
            int cur = q.poll();

            for(int next : graph[cur]) {
                indegrees[next]--;
                endTimes[next] = Math.max(endTimes[next], endTimes[cur]);    //  next가 완전히 끝나려면 cur 작업이 먼저 끝나야 함, 선행 작업들 중 가장 오래 걸리는 작업 기준으로 함

                if(indegrees[next] == 0) {
                    q.offer(next);
                    endTimes[next] += times[next];  //  next의 선행 작업들이 모두 끝나고, next 자체의 작업 시간 추가
                    maxTime = Math.max(maxTime, endTimes[next]);
                }
            }
        }

        System.out.println(maxTime);
    }
}   //  Main-class-end
```

**CO{)E TREE 갈 수 있는 경우의 수**

[https://www.codetree.ai/missions/9/problems/possible-path-of-travel/description](https://www.codetree.ai/missions/9/problems/possible-path-of-travel/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/possible-path-of-travel/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MOD = 1_000_000_007;
    private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static int n, m;
    private static List<Integer>[] graph;
    private static int[] cnts;
    private static boolean[] visited;

    public static void main(String[] args) throws IOException {
        StringTokenizer tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        graph = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) {
            graph[i] = new ArrayList<>();
        }
        cnts = new int[n + 1];
        visited = new boolean[n + 1];

        for (int i = 0; i < m; i++) {
            tokens = new StringTokenizer(br.readLine());
            int prev = Integer.parseInt(tokens.nextToken());
            int next = Integer.parseInt(tokens.nextToken());
            graph[prev].add(next);
        }

        // 초기화
        cnts[1] = 1;  // 1번 노드에서 1번 노드로의 경로는 하나뿐임

        // DFS 호출
        dfs(1);

        System.out.println(cnts[n]);
    }

    private static void dfs(int cur) {
        visited[cur] = true;
        for (int next : graph[cur]) {
            if (!visited[next]) {
                dfs(next);
            }
            cnts[next] = (cnts[next] + cnts[cur]) % MOD;
        }
        visited[cur] = false;  // 노드 방문 완료 후 방문 해제
    }
}

```