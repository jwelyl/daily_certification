# 24_11_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12978 스크루지 민호 2

[](https://www.acmicpc.net/problem/12978)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
	private static final int ROOT = 1;
	private static final int NO   = 0;	//	정점을 칠하지 않을 경우 0
	private static final int YES  = 1;	//	정점을 칠할 경우 1
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	
	private static List<Integer>[] tree;
	private static boolean[] visited;
	private static int[][] dp;
	
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        visited = new boolean[N + 1];
        dp = new int[N + 1][2];
        
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
        
        dfs(ROOT);
        
        System.out.println(Math.min(dp[ROOT][NO], dp[ROOT][YES]));
    }   //  main-end
    
    private static void dfs(int v) {
    	dp[v][YES] = 1;	//	v에 칠하는 경우 일단 1
    	visited[v] = true;
    	
    	for(int next : tree[v]) {
    		if(!visited[next]) {	//	자식 정점일 경우
    			dfs(next);
    			
    			//	v를 칠했을 경우, next는 칠해도 되고 칠하지 않아도 됨. 더 작은 값을 선택
    			dp[v][YES] += Math.min(dp[next][NO], dp[next][YES]);
    			//	v를 칠하지 않았을 경우, next는 칠해야 함
    			dp[v][NO] += dp[next][YES];
    		}
    	}
    }
}   //  Main-class-end
```