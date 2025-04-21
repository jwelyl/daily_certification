# 25_04_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23327 리그전 오브 레전드

[23327번: 리그전 오브 레전드](http://boj.ma/23327/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int Q;
    
    private static int[] prefixSum;
    private static long[] prefixNumSum;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        Q = Integer.parseInt(st.nextToken());
        
        prefixSum = new int[N + 1];
        prefixNumSum = new long[N + 1];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            int num = Integer.parseInt(st.nextToken());
            prefixSum[i] = prefixSum[i - 1] + num;
            prefixNumSum[i] = prefixNumSum[i - 1] + (long)prefixSum[i] * num;
        }
        
        for(int q = 0; q < Q; q++) {
            st = new StringTokenizer(br.readLine());
            int l = Integer.parseInt(st.nextToken());
            int r = Integer.parseInt(st.nextToken());
        
            long answer = (long)prefixSum[r] * (prefixSum[r - 1] - prefixSum[l - 1])
                - (prefixNumSum[r - 1] - prefixNumSum[l - 1]);
        
            sb.append(answer).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end    
```

### BOJ 1068 트리

[1068번: 트리](http://boj.ma/1068/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int root = -1;
    
    private static int N;
    private static List<Integer>[] tree;
    private static boolean[] visited;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        tree = new ArrayList[N];
        for(int v = 0; v < N; v++)
            tree[v] = new ArrayList<>();
        visited = new boolean[N];
        
        st = new StringTokenizer(br.readLine());
        for(int v = 0; v < N; v++) {
            int p = Integer.parseInt(st.nextToken());
            
            if(p == -1)
                root = v;
            else {
                tree[v].add(p);
                tree[p].add(v);
            }
        }
        
        visited[Integer.parseInt(br.readLine())] = true;
        
        if(!visited[root])
            dfs(root);
        
        System.out.println(answer);
    }    //    main-end
    
    private static void dfs(int v) {
        int nChild = 0;    //    자식 노드 개수
        
        visited[v] = true;
        for(int nv : tree[v]) {
            if(!visited[nv]) {
                nChild++;
                dfs(nv);
            }
        }
        
        if(nChild == 0)    //    자식 노드가 0개일 경우
            answer++;
    }
}    //    Main-class-end
```