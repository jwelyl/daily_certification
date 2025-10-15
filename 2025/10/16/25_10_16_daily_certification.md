# 25_10_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5829 Luxury River Cruise

[5829번: Luxury River Cruise](http://boj.ma/5829/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int L = -1;
    private static final int R =  1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    시퀀스 길이
    private static int K;    //    시퀀스 반복 횟수
    
    private static int[] left;            //    left[i] : i번 정점이 왼쪽으로 이어진 정점
    private static int[] right;           //    right[i] : i번 정점이 오른쪽으로 이어진 정점
    private static int[] sequence;        //    시퀀스
    private static int[] dst;             //    dst[i] : i번 정점이 한번의 시퀀스를 거쳤을때 도착하는 정점
    
    private static boolean[] visited;
    private static int[] dist;
    private static int cycleStart = -1;    //    사이클 시작점 (1에서 가장 먼저 도착되는 사이클에 포함된 정점)
    private static int cycleEnd = -1;      //    사이클 끝점
    private static int cycleLength = 0;    //    사이클 길이
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        left = new int[N + 1];
        right = new int[N + 1];
        sequence = new int[M];
        dst = new int[N + 1];
        visited = new boolean[N + 1];
        dist = new int[N + 1];
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            left[i] = Integer.parseInt(st.nextToken());
            right[i] = Integer.parseInt(st.nextToken());
        }
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < M; i++)
            sequence[i] = st.nextToken().charAt(0) == 'L' ? L : R;

        for(int i = 1; i <= N; i++)
            getDst(i);

        dfs(1, 0);
        K -= dist[cycleStart];    //    1에서 cycle 시작 정점까지의 거리 빼기
        K %= cycleLength;         //    사이클 돌고 난 나머지
        
        answer = cycleStart;
        for(int i = 0; i < K; i++)
            answer = dst[answer];
        
        System.out.println(answer);
    }    //    main-end
    
    private static void dfs(int v, int d) {
        visited[v] = true;
        dist[v] = d;
        
        if(visited[dst[v]]) {
            cycleStart = dst[v];
            cycleEnd = v;
            cycleLength = dist[v] - dist[dst[v]] + 1;
        }
        else
            dfs(dst[v], d + 1);
    }
    
    private static void getDst(int start) {
        int cur = start;
        
        for(int dir : sequence) {
            if(dir == L)
                cur = left[cur];
            else
                cur = right[cur];
        }
        
        dst[start] = cur;
    }
}    //    Main-class-end
```

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int L = -1;
    private static final int R =  1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;        //    정점 개수
    private static int M;        //    시퀀스 길이
    private static int K;        //    시퀀스 반복 횟수
    private static int MAX_D;
    
    private static int[] left;            //    left[i] : i번 정점이 왼쪽으로 이어진 정점
    private static int[] right;           //    right[i] : i번 정점이 오른쪽으로 이어진 정점
    private static int[] sequence;        //    시퀀스
    //    sparseTable[d][i] : i 정점에서 2^d번 시퀀스 이동했을 때의 정점
    private static int[][] sparseTable;
    
    private static int answer = 1;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        MAX_D = (int)Math.ceil(Math.log(K) / Math.log(2)) + 1;
        
        left = new int[N + 1];
        right = new int[N + 1];
        sequence = new int[M];
        sparseTable = new int[MAX_D + 1][N + 1];
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            left[i] = Integer.parseInt(st.nextToken());
            right[i] = Integer.parseInt(st.nextToken());
        }
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < M; i++)
            sequence[i] = st.nextToken().charAt(0) == 'L' ? L : R;

        for(int i = 1; i <= N; i++)
            getDst(i);
        
        makeSparseTable();
        
        for(int d = MAX_D; d >= 0; d--) {
            if((K & (1 << d)) != 0)
                answer = sparseTable[d][answer];
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static void makeSparseTable() {
        for(int d = 1; d <= MAX_D; d++) {
            for(int i = 1; i <= N; i++)
                sparseTable[d][i] = sparseTable[d - 1][sparseTable[d - 1][i]];
        }
    }

    private static void getDst(int start) {
        int cur = start;
        
        for(int dir : sequence) {
            if(dir == L)
                cur = left[cur];
            else
                cur = right[cur];
        }
        
        sparseTable[0][start] = cur;
    }
}    //    Main-class-end
```

### BOJ 24524 아름다운 문자열

[24524번: 아름다운 문자열](http://boj.ma/24524/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    private static String S;
    private static String T;
    
    private static final Queue<Integer>[] queues = new ArrayDeque[26];
    
    private static int size;
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        for(int i = 0; i < 26; i++)
            queues[i] = new ArrayDeque<>();
        
        S = br.readLine();
        T = br.readLine();
        size = S.length();

        for(int i = 0; i < S.length(); i++) {
            char ch = S.charAt(i);
            queues[ch - 'a'].offer(i);
        }
        
        OUTER:
        while(T.length() <= size) {
            int prevIdx = -1;
            
            for(int i = 0; i < T.length(); i++) {
                char ch = T.charAt(i);
                
                while(!queues[ch - 'a'].isEmpty() && queues[ch - 'a'].peek() < prevIdx)
                    queues[ch - 'a'].poll();
                
                if(queues[ch - 'a'].isEmpty())
                    break OUTER;
                
                prevIdx = queues[ch - 'a'].poll();
            }
            
            answer++;
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```