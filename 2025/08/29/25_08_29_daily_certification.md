# 25_08_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10273 고대 동굴 탐사

[10273번: 고대 동굴 탐사](http://boj.ma/10273/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
import java.util.Stack;

public class Main {
    private static final int ROOT = 1;
    private static final int NONE = 0;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스
    
    private static int N;    //    동굴 수
    private static int E;    //    동굴 사이 통로 수
    
    //    values[v] : v번째 동굴 가치
    private static int[] values;
    private static List<int[]>[] graph;
    
    private static int[] parents;
    
    //    dp[v] : v번째 동굴까지 왔을떄 최대 가치
    private static int[] dp;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            st = new StringTokenizer(br.readLine());
            N = Integer.parseInt(st.nextToken());
            E = Integer.parseInt(st.nextToken());
            
            values = new int[N + 1];
            graph = new ArrayList[N + 1];
            
            parents = new int[N + 1];
            dp = new int[N + 1];
            
            st = new StringTokenizer(br.readLine());
            for(int v = 1; v <= N; v++) {
                values[v] = Integer.parseInt(st.nextToken());
                graph[v] = new ArrayList<>();
                dp[v] = Integer.MIN_VALUE;
            }
            
            for(int e = 0; e < E; e++) {
                st = new StringTokenizer(br.readLine());
                int v1 = Integer.parseInt(st.nextToken());
                int v2 = Integer.parseInt(st.nextToken());
                int c = Integer.parseInt(st.nextToken());
                
                graph[v1].add(new int[] {v2, c});
            }
            
            bfs();
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void bfs() {
        Queue<Integer> queue = new LinkedList<>();
        int maxProfit = 0;                        //    최대 가치
        int maxProfitV = NONE;                    //    최대 가치가 되는 도착 동굴
        Stack<Integer> path = new Stack<>();      //    그때 동굴 경로
        
        dp[ROOT] = values[ROOT];
        maxProfit = dp[ROOT];
        maxProfitV = ROOT;
        queue.offer(ROOT);
        
        while(!queue.isEmpty()) {
            int cur = queue.poll();
            
            for(int[] edge : graph[cur]) {
                int next = edge[0];
                int cost = edge[1];
                
                if(dp[cur] - cost + values[next] > dp[next]) {
                    dp[next] = dp[cur] - cost + values[next];
                    parents[next] = cur;
                    queue.offer(next);
                    
                    if(dp[next] > maxProfit) {
                        maxProfit = dp[next];
                        maxProfitV = next;
                    }
                }
            }
        }
        
        while(maxProfitV != NONE) {
            path.add(maxProfitV);
            maxProfitV = parents[maxProfitV];
        }
        
        sb.append(maxProfit).append(" ").append(path.size()).append("\n");
        while(!path.isEmpty())
            sb.append(path.pop()).append(" ");
        sb.append("\n");
    }
}    //    Main-class-end
```

### BOJ 1030 프렉탈 평면

[1030번: 프렉탈 평면](http://boj.ma/1030/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final char W = '0';
    private static final char B = '1';
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int S;
    private static int N;
    private static int K;
    private static int R1;
    private static int R2;
    private static int C1;
    private static int C2;
    
    private static char[][] fractal;
    private static char[][] answer;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        S = Integer.parseInt(st.nextToken());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        R1 = Integer.parseInt(st.nextToken());
        R2 = Integer.parseInt(st.nextToken());
        C1 = Integer.parseInt(st.nextToken());
        C2 = Integer.parseInt(st.nextToken());
        
        fractal = new char[N][N];
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				fractal[y][x] = W;
		}
		
		for(int y = (N - K) / 2; y <= (N - K) / 2 + K - 1; y++) {
			for(int x = (N - K) / 2; x <= (N - K) / 2 + K - 1; x++)
				fractal[y][x] = B;
		}
        
        answer = new char[R2 - R1 + 1][C2 - C1 + 1];
        for(int y = 0; y <= R2 - R1; y++) {
        	for(int x = 0; x <= C2 - C1; x++)
        		answer[y][x] = B;
        }
        
        recursion(S, 0, 0, (int)Math.pow(N, S) - 1, (int)Math.pow(N, S) - 1, R1, C1, R2, C2);
        
        for(int r = 0; r <= R2 - R1; r++) {
            for(int c = 0; c <= C2 - C1; c++)
                sb.append(answer[r][c]);
            sb.append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void recursion(int s, int sy, int sx, int ey, int ex, int r1, int c1, int r2, int c2) {
    	if(s <= 1) {
    		for(int y = r1; y <= r2; y++) {
    			for(int x = c1; x <= c2; x++)
    				answer[y - R1][x - C1] = fractal[y - sy][x - sx];
    		}

            return;
        }
    	
    	int len = (int)Math.pow(N, s - 1);
    	
    	for(int y = 0; y < N; y++) {
    		for(int x = 0; x < N; x++) {
     			if(fractal[y][x] == W) {
     				int ssy = sy + len * y;
     				int ssx = sx + len * x;
     				int eey = ssy + len - 1;
     				int eex = ssx + len - 1;
     				
     	            int[] dup = intersect(ssy, ssx, eey, eex, r1, c1, r2, c2);
     	            if(dup[0] != -1 && dup[1] != -1 && dup[2] != -1 && dup[3] != -1)    //    겹치는 부분이 있을 경우
     	                recursion(s - 1, ssy, ssx, eey, eex, dup[0], dup[1], dup[2], dup[3]);
     			}
     		}
    	}
    }
    
    //	좌상단 좌표 (sy, sx), 우하단 좌표 (ey, ex)인 직사각형 R1
    //	좌상단 좌표 (r1, c1), 우하단 좌표 (r2, c2)인 직사각형 R2
    //	두 직사각형의 겹치는 영역이 존재할 경우 그 직사각형 R의 좌상단 {y좌표, 좌상단 x좌표, 우하단 y좌표, 우하단 x좌표} 반환
    private static int[] intersect(int sy, int sx, int ey, int ex, int r1, int c1, int r2, int c2) {
    	int[] res = {-1, -1, -1, -1};	//	겹치는 부분이 없을 경우 모든 좌표 -1
    	
    	int iy1 = Math.max(sy, r1);
    	int ix1 = Math.max(sx, c1);
    	int iy2 = Math.min(ey, r2);
    	int ix2 = Math.min(ex, c2);
    	
    	if(iy1 <= iy2 && ix1 <= ix2) {	//	겹치는 영역이 있을 경우
    		res[0] = iy1;
    		res[1] = ix1;
    		res[2] = iy2;
    		res[3] = ix2;
    	}
    	
    	return res;
    }
}    //    Main-class-end
```