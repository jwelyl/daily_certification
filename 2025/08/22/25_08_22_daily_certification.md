# 25_08_22_daily_certification

# To Do List

```
[#234 koreii] 데일리인증 20250822
1. 프로젝트
- Session, JWT
2. 알고리즘 문제 풀이
- Tree DP (BOJ 12900 Cheating a Boolean Tree)
```

```
[#053] 데일리인증 20250821 (234 / 365)
1. 서류 작성
2. 알고리즘 문제 풀이
- Tree DP (BOJ 12900 Cheating a Boolean Tree)
- Bruteforcing, Prefix Sum, HashMap (BOJ 1184 귀농)
```

# Problem Solving (Algorithm & SQL)

### BOJ 12900 Cheating a Boolean Tree

[12900번: Cheating a Boolean Tree](http://boj.ma/12900/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 1_000_000_000;	//	몇개를 바꿔도 원하는 값으로 바꿀 수 없음
	
	private static final int OR = 0;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;
    private static int N;	//	트리 정점 개수
    private static int V;	//	만들고자 하는 트리 값
    
    private static int internal;
    
    //	nodes[v] : v번째 노드의 {값, GATE}
    private static int[][] nodes;
    
    //	changable[v] : v번째 노드의 GATE를 변경할 수 있으면 true
    private static boolean[] changeable;

    //	dp[v][value] : v를 루트로 하는 서브트리의 값을 value로 변경하기 위해 바꿔줘야 하는 internal node 최소 개수
    private static int[][] dp;
    
    public static void main(String[] args) throws IOException {
    	T = Integer.parseInt(br.readLine());
    	
    	for(int tc = 1; tc <= T; tc++) {
    		st = new StringTokenizer(br.readLine());
    		N = Integer.parseInt(st.nextToken());
    		V = Integer.parseInt(st.nextToken());
    		
    		internal = N / 2;
    		nodes = new int[N + 1][2];
    		changeable = new boolean[N + 1];
    		
    		//	internal node 입력
    		for(int v = 1; v <= internal; v++) {
    			st = new StringTokenizer(br.readLine());
        		int gate = Integer.parseInt(st.nextToken());
        		boolean change = Integer.parseInt(st.nextToken()) == 1;
        		
        		nodes[v][1] = gate;
        		changeable[v] = change;
    		}
    		
    		//	leaf node 입력
    		for(int v = internal + 1; v <= N; v++)
    			nodes[v][0] = Integer.parseInt(br.readLine());
    		
    		dp = new int[N + 1][2];
    		for(int v = 1; v <= N; v++) {
    			dp[v][0] = INF;
    			dp[v][1] = INF;
    		}
    		
    		dfs(1);
    		
    		sb.append("Case #").append(tc).append(": ").append(dp[1][V] == INF ? "IMPOSSIBLE" : dp[1][V]).append("\n");
    	}
    	
    	System.out.print(sb);
    }    //    main-end
    
    private static void dfs(int v) {
    	if(internal + 1 <= v && v <= N) {	//	leaf node일 경우
    		dp[v][nodes[v][0]] = 0;			//	v 노드의 값으로는 어떤 변화도 주지 않고 변경 가능
    		return;
    	}
    	
    	int gate = nodes[v][1];
    	int lc = v * 2;				//	v의 왼쪽 자식
    	int rc = v * 2 + 1;			//	v의 오른쪽 자식

    	dfs(lc);
    	dfs(rc);
    	
    	int lc0 = dp[lc][0];
		int lc1 = dp[lc][1];	//	lc를 루트로 하는 서브트리 값을 0/1로 하기 위한 최소 변경 횟수
		int rc0 = dp[rc][0];
		int rc1 = dp[rc][1];	//	rc를 루트로 하는 서브트리 값을 0/1로 하기 위한 최소 변경 횟수
    	
		//	v를 루트로 하는 서브트리 값을 0으로 하기 위해서는
		//	0 OR 0 또는 1 AND 0 또는 0 AND 1 또는 0 AND 0
		//  v를 루트로 하는 서브트리 값을 1으로 하기 위해서는
		//	1 OR 0 또는 0 OR 1 또는 1 OR 1 또는 1 AND 1
    	if(changeable[v]) {	//	v 노드의 gate를 변경할 수 있을 경우
    		if(gate == OR) {	//	v의 게이트가 OR일 경우
    			//	OR일때 0으로 만들기
    			dp[v][0] = Math.min(dp[v][0], lc0 + rc0);	//	gate를 변경하지 않을 경우
    			dp[v][0] = Math.min(dp[v][0], Math.min(lc1 + rc0 + 1, Math.min(lc0 + rc1 + 1, lc0 + rc0 + 1)));		//	gate를 AND로 변경할 경우
    		
    			//	OR일때 1로 만들기
    			dp[v][1] = Math.min(dp[v][1], Math.min(lc1 + rc0, Math.min(lc0 + rc1, lc1 + rc1)));	//	gate를 변경하지 않을 경우
    			dp[v][1] = Math.min(dp[v][1], lc1 + rc1 + 1);	//	gate를 AND로 변경할 경우
    		}
    		else {	//	v의 게이트가 AND일 경우
    			//	AND일때 0으로 만들기
    			dp[v][0] = Math.min(dp[v][0], Math.min(lc1 + rc0, Math.min(lc0 + rc1, lc0 + rc0)));	//	gate를 변경하지 않을 경우
    			dp[v][0] = Math.min(dp[v][0], lc0 + rc0 + 1);	//	gate를 OR로 변경할 경우
    		
    			//	AND일때 1로 만들기
    			dp[v][1] = Math.min(dp[v][1], lc1 + rc1);	//	gate를 변경하지 않을 경우
    			dp[v][1] = Math.min(dp[v][1], Math.min(lc1 + rc0 + 1, Math.min(lc0 + rc1 + 1, lc1 + rc1 + 1)));	//	gate를 OR로 변경할 경우
    		}
    	}
    	else {
    		if(gate == OR) {	//	v의 게이트가 OR일 경우
    			//	OR일때 0으로 만들기
    			dp[v][0] = Math.min(dp[v][0], lc0 + rc0);
    			//	OR일때 1로 만들기
    			dp[v][1] = Math.min(dp[v][1], Math.min(lc1 + rc0, Math.min(lc0 + rc1, lc1 + rc1)));
    		}
    		else {	//	v의 게이트가 AND일 경우
    			//   AND일때 0으로 만들기
    			dp[v][0] = Math.min(dp[v][0], Math.min(lc1 + rc0, Math.min(lc0 + rc1, lc0 + rc0)));	//	gate를 변경하지 않을 경우
    			//	AND일때 1로 만들기
    			dp[v][1] = Math.min(dp[v][1], lc1 + rc1);
    		}
    	}
    }
}    //    Main-class-end
```

### BOJ 1184 귀농

[1184번: 귀농](http://boj.ma/1184/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int[][] prefixSum;
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        prefixSum = new int[N + 1][N + 1];
        
        for(int y = 1; y <= N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 1; x <= N; x++)
                prefixSum[y][x] = prefixSum[y - 1][x] + prefixSum[y][x - 1] - prefixSum[y - 1][x - 1] + Integer.parseInt(st.nextToken());
        }
   
        for(int y = 1; y <= N; y++) {
            for(int x = 1; x <= N; x++) {
                if(isIn(y + 1, x + 1)) {
                    Map<Integer, Integer> map = new HashMap<>();

                    for(int yy = 1; yy <= y; yy++) {
                        for(int xx = 1; xx <= x; xx++) {
                            int ps = partSum(yy, xx, y, x);
                            
                            map.put(ps, map.getOrDefault(ps, 0) + 1);
                        }
                    }
                    
                    for(int yy = y + 1; yy <= N; yy++) {
                        for(int xx = x + 1; xx <= N; xx++) {
                            int ps = partSum(y + 1, x + 1, yy, xx);
                            
                            answer += map.getOrDefault(ps, 0);
                        }
                    }
                }
                
                if(isIn(y - 1, x + 1)) {
                    Map<Integer, Integer> map = new HashMap<>();
                    
                    for(int yy = y; yy <= N; yy++) {
                        for(int xx = 1; xx <= x; xx++) {
                            int ps = partSum(y, xx, yy, x);
                            
                            map.put(ps, map.getOrDefault(ps, 0) + 1);
                        }
                    }
                    
                    for(int yy = 1; yy <= y - 1; yy++) {
                        for(int xx = x + 1; xx <= N; xx++) {
                            int ps = partSum(yy, x + 1, y - 1, xx);
                            
                            answer += map.getOrDefault(ps, 0);
                        }
                    }
                }
            }
        }
        
        System.out.println(answer);
    }    //    main-end
    
    //    좌상단 (y1, x1), 우하단 (y2, x2)인 직사각형 부분합
    private static int partSum(int y1, int x1, int y2, int x2) {
        return prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1];
    }
    
    private static boolean isIn(int y, int x) {
        return (1 <= y && y <= N) && (1 <= x && x <= N);
    }
}    //    Main-class-end
```