# 25_09_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14673 Crush Fever

[14673번: Crush Fever](http://boj.ma/14673/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int EMPTY = 0;
	private static final int MAX_PUSH = 3;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;	//	퍼즐 높이
    private static int M;	//	퍼즐 너비
    
    private static int[][] origPuzzle;	//	원본 퍼즐
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	M = Integer.parseInt(st.nextToken());
    	N = Integer.parseInt(st.nextToken());
    	
    	origPuzzle = new int[N][M];
    	
    	for(int y = 0; y < N; y++) {
    		st = new StringTokenizer(br.readLine());
    		for(int x = 0; x < M; x++)
    			origPuzzle[y][x] = Integer.parseInt(st.nextToken());
    	}
    	
    	backtracking(0, new int[MAX_PUSH]);
    	
    	System.out.println(answer);
    }    //    main-end
    
    private static int[][] copy() {
    	int[][] copied = new int[N][M];
    	
    	for(int y = 0; y < N; y++) {
    		for(int x = 0; x < M; x++)
    			copied[y][x] = origPuzzle[y][x];
    	}
    	
    	return copied;
    }
    
    private static int bfs(int y, int x, int[][] copiedPuzzle) {
    	int num = copiedPuzzle[y][x];
    	
    	if(num == 0)
    		return 0;
    	
    	Queue<int[]> queue = new LinkedList<>();
    	int res = 1;
    	copiedPuzzle[y][x] = EMPTY;
    	queue.offer(new int[] {y, x});
    	
    	while(!queue.isEmpty()) {
    		int[] cur = queue.poll();
    		int cy = cur[0];
    		int cx = cur[1];
    		
    		for(int d = 0; d < 4; d++) {
    			int ny = cy + dy[d];
    			int nx = cx + dx[d];
    			
    			if(isIn(ny, nx) && num == copiedPuzzle[ny][nx]) {
    				copiedPuzzle[ny][nx] = EMPTY;
    				queue.offer(new int[] {ny, nx});
    				res++;
    			}
    		}
    	}
    	
    	return res;
    }
    
    private static void drop(int[][] copiedPuzzle) {
    	for(int x = 0; x < M; x++) {
    		for(int ey = 1; ey < N; ey++) {
    			for(int sy = N - 1; sy >= ey; sy--) {
    				if(copiedPuzzle[sy][x] == EMPTY) {
    					copiedPuzzle[sy][x] = copiedPuzzle[sy - 1][x];
    					copiedPuzzle[sy - 1][x] =  EMPTY;
    				}
    			}
    		}
    	}
    }
    
    private static void backtracking(int nth, int[] orders) {
    	if(nth == MAX_PUSH) {
    		int[][] copiedPuzzle = copy();
    		int score = 0;
    		
    		for(int i = 0; i < MAX_PUSH; i++) {
    			int push = orders[i];
    			int y = push / M;
    			int x = push % M;
    			
    			int cnt = bfs(y, x, copiedPuzzle);
    			score += cnt * cnt;
    			drop(copiedPuzzle);
    		}
    		
    		answer = Math.max(answer, score);
    		
    		return;
    	}
    	
    	for(int num = 0; num < N * M; num++) {
    		orders[nth] = num;
    		backtracking(nth + 1, orders);
    	}
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```