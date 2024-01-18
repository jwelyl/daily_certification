# 24_01_18_daily_certification

```
[koreii] #18 데일리인증
20240118
Spring

Computer Network

알고리즘
오일러 경로 학습 + 문제 풀이 (시간 초과 해결)
```

# Problem Solving (Algorithm & SQL)

**BOJ 1199 오일러 회로**

[1199번: 오일러 회로](https://www.acmicpc.net/problem/1199)

어떤 정점에서 시작해 모든 간선을 딱 한 번씩만 지나서 시작 정점으로 돌아오는 경로를 출력하는 문제이다.

Kotlin으로 풀이해봤는데 시간 초과를 해결할 수 없어서 같은 코드를 Java로 변경해서 제출했다.

Java 코드도 dfs에서 반복문을 돌때 반복 횟수를 n으로 접근하면 시간 초과가 발생했다. graph.length로 접근하면 시간초과를 피할 수 있었다.

출력할 때도 StringBuilder의 append를 쓰면 메모리를 절약하는 대신 시간이 좀 더 오래걸렸고, + 로 concat하면 시간은 조금 줄어드는 대신 메모리를 더 소모했다.

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int N = 0;
    private static int[][] graph;

    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());

        graph = new int[N][N];
        for(int i = 0; i < N; i++) {
            int cnt = 0;
            tokens = new StringTokenizer(br.readLine());
            for(int j = 0; j < N; j++) {
                graph[i][j] = Integer.parseInt(tokens.nextToken());
                cnt += graph[i][j];
            }

            if(cnt % 2 != 0) {
                System.out.println(-1);
                return;
            }
        }

        dfs(0);
        System.out.print(sb);
    }

    private static void dfs(int v) {
        for(int nv = 0; nv < graph.length; nv++) {
            while(graph[v][nv] > 0) {
                graph[v][nv]--;
                graph[nv][v]--;
                dfs(nv);
            }
        }

        sb.append((v + 1) + " ");
    }
}
```