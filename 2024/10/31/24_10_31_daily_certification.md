# 24_10_31_daily_certification

```
[#305 koreii] 데일리인증 202410131
1. Spring Core Advanced
- JDK 동적 프록시
- CGLIB
2. 컴퓨터공학 이론
- DBCP
- TCP 3-way Handshake
- TCP 연결 종료
- loopback
- Indexing
- B+Tree
- 주소창에 www.naver.com을 입력하면 발생하는 일
3. 코딩 테스트 대비 알고리즘 학습
-  Tree DP, DFS, Postorder (25515 트리 노드 합의 최댓값, 31740 Split the SSHS 3)
```

# Problem Solving (Algorithm & SQL)

### BOJ 25515 트리 노드 합의 최댓값

[](https://www.acmicpc.net/problem/25515)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final long NONE = 100_000L * 100_000L + 1;
    private static final int ROOT = 0;    //    루트는 0
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;            //    트리 노드 개수
    private static int[] price;      //    price[i] : i 노드의 값
    private static long[] dp;        //    dp[i] : i 노드를 반드시 방문했을때, i 노드를 루트로 하는 서브트리의 노드 합의 최댓값
    
    private static List<Integer>[] tree;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        price = new int[N];
        dp = new long[N];
        Arrays.fill(dp, NONE);
        
        tree = new ArrayList[N];
        for(int v = 0; v < N; v++)
            tree[v] = new ArrayList<>();
        
        for(int m = 0; m < N - 1; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            tree[v1].add(v2);
        }
        
        st = new StringTokenizer(br.readLine());
        for(int v = 0; v < N; v++)
            price[v] = Integer.parseInt(st.nextToken());
        
        System.out.println(dp(ROOT));
    }    //    main-end
    
    private static long dp(int v) {
        if(dp[v] == NONE) {
            dp[v] = price[v];    //    v 노드는 반드시 방문해야 함
            
            for(int child : tree[v]) {    //    v 노드의 자식 노드 child
                long add = dp(child);    //    child를 루트로 하는 서브트리의 노드 합 최댓값
                
                if(add >= 0)    //    child를 루트로 하는 서브트리를 더하는게 이득일 경우
                    dp[v] += add;
            }
        }
        
        return dp[v];
    }
}    //    Main-class-end
```

### BOJ 31740 Split the SSHS 3

[](https://www.acmicpc.net/problem/31740)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;

public class Main {
    private static final int NONE = Integer.MAX_VALUE;    //    미방문 배열
    private static final int ROOT = 1;    //    루트 노드
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    노드 개수
    private static int[] price;        //    각 노드의 중요도
    private static int[] parents;      //    parents[v] : 1을 루트로 하는 트리에서 v 노드의 부모 노드, v = ROOT일 경우 parents[ROOT] = 0
    private static int[] dp;           //    dp[v] : v를 루트로 하는 서브트리의 모든 노드의 중요도 합
    
    private static int minDiff = Integer.MAX_VALUE;    //    두 개의 트리로 분할했을때 두 트리의 중요도 차의 최솟값
    private static int minDiffNode = 0;                //    그때의 분할 노드
    
    private static List<Integer>[] tree;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        price = new int[N + 1];
        parents = new int[N + 1];
        dp = new int[N + 1];
        Arrays.fill(dp, NONE);
        
        tree = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            tree[v] = new ArrayList<>();
        
        for(int m = 0; m < N - 1; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            tree[v1].add(v2);
            tree[v2].add(v1);
        }
        
        for(int v = 1; v <= N; v++)
            price[v] = Integer.parseInt(br.readLine());
        
        dfs(ROOT);
        
        for(int v = 2; v <= N; v++) {
            int sum1 = dp[v];
            int sum2 = dp[ROOT] - dp[v];
            
            if(Math.abs(sum2 - sum1) < minDiff) {    //    v와 parents[v] 사이의 간선을 자르는게 최선일 경우
                minDiff = Math.abs(sum2 - sum1);
                minDiffNode = v;
            }
        }
        
        System.out.println(minDiff);
        System.out.println(minDiffNode + " " +  parents[minDiffNode]);
    }    //    main-end
    
    private static void dfs(int v) {
        dp[v] = price[v];    //    일단 자기 자신 값 설정
        
        for(int next : tree[v]) {    //    v와 연결된 다른 노드 next
            if(dp[next] == NONE) {    //    dp[next]가 NONE이라는것은 아직 미방문했다는 뜻, 즉 v의 자식이 next임
                parents[next] = v;
                dfs(next);    //    next 방문
                dp[v] += dp[next];    //    next를 루트로 하는 서브트리의 노드 중요도 합을 더하기
            }
        }
    }
}    //    Main-class-end
```