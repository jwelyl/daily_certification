# 25_02_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2342 Dance Dance Revolution

[2342번: Dance Dance Revolution](http://boj.ma/2342/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 100_000 * 4 + 1;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static final List<Integer> orders = new ArrayList<>();
    
    private static final int[][] force = {
    		{0, 2, 2, 2, 2},
    		{0, 1, 3, 4, 3},
    		{0, 3, 1, 3, 4},
    		{0, 4, 3, 1, 3},
    		{0, 3, 4, 3, 1}
    };
    
    private static int[][][] dp;	//	dp[i][l][r] : 왼쪽발이 l, 오른쪽발이 r에 있을때 i번까지 누르는데 최소 힘
    
    private static int answer = MAX;
    
    public static void main(String[] args) throws IOException {
    	orders.add(0);
    	
    	st = new StringTokenizer(br.readLine());
    	while(true) {
    		int pos = Integer.parseInt(st.nextToken());
    		
    		if(pos == 0)
    			break;
    		
    		orders.add(pos);
    	}
    	
    	dp = new int[orders.size()][5][5];
    	for(int i = 0; i < orders.size(); i++) {
    		for(int j = 0; j < 5; j++)
    			for(int k = 0; k < 5; k++)
    				dp[i][j][k] = MAX;
    	}
    	
    	dp[0][0][0] = 0;
    	
    	for(int i = 1; i < orders.size(); i++) {
    		int target = orders.get(i);	//	target을 밟아야함
    		
    		for(int l = 0; l < 5; l++) {
    			for(int r = 0; r < 5; r++)
    				dp[i][target][r] = Math.min(dp[i][target][r], dp[i - 1][l][r] + force[l][target]);
    		}
    		
    		for(int l = 0; l < 5; l++) {
    			for(int r = 0; r < 5; r++)
    				dp[i][l][target] = Math.min(dp[i][l][target], dp[i - 1][l][r] + force[r][target]);
    		}
    	}
    	
    	for(int l = 0; l < 5; l++) {
    		for(int r = 0; r < 5; r++)
    			answer = Math.min(answer, dp[orders.size() - 1][l][r]);
    	}
    	
    	System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1103 게임

[1103번: 게임](http://boj.ma/1103/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MIN_VALUE;
	private static final int INF = Integer.MAX_VALUE;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static char[][] board;
    private static boolean[][] visited;
    private static long[][] dp;
    
    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	N = Integer.parseInt(st.nextToken());
    	M = Integer.parseInt(st.nextToken());
    	
    	board = new char[N][M];
    	visited = new boolean[N][M];
    	dp = new long[N][M];
    	
    	for(int y = 0; y < N; y++) {
    		board[y] = br.readLine().toCharArray();
    		for(int x = 0; x < M; x++)
    			dp[y][x] = NONE;
    	}
    	
    	dp(0, 0);
    	
    	System.out.println(dp[0][0] >= INF ? -1 : dp[0][0]);
    }    //    main-end
    
    private static long dp(int y, int x) {
    	if(dp[y][x] != NONE)
    		return dp[y][x];
    	
    	visited[y][x] = true;
    	
    	if(board[y][x] == 'H')	//	(y, x)가 구멍이면 더 나아갈 수 없음
    		dp[y][x] = 0;
    	else {
    		dp[y][x] = 1;		//	(y, x)가 구멍이 아니라면 일단 한번은 움직일 수 있음
    		
    		for(int d = 0; d < 4; d++) {
    			int ny = y + dy[d] * (board[y][x] - '0');
    			int nx = x + dx[d] * (board[y][x] - '0');	//	다음에 이동할 칸 (ny, nx)
    			
    			if(isIn(ny, nx)) {
	    			if(visited[ny][nx]) {	//	이미 방문한 칸을 또 방문하게 되는 경우, 사이클 발생
	    				dp[y][x] = INF;		//	무한히 움직일 수 있음
	    				break;
	    			}
	    			
	    			//	(ny, nx)까지 이동한 후 (ny, nx)에서 최대로 이동할 수 있는 횟수를 더함
	    			dp[y][x] =	Math.max(dp[y][x], 1L + dp(ny, nx));
    			}
    		}
    	}
    	
    	visited[y][x] = false;
    	return dp[y][x];
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```