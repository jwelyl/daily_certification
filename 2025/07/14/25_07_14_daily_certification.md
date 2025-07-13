# 25_07_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2452 그리드 게임

[2452번: 그리드 게임](http://boj.ma/2452/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.Set;
import java.util.StringTokenizer;
 
public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;	//	행 개수
	private static int M;	//	열 개수
	
	private static final List<Pos> startPosList = new ArrayList<>();
	
	private static int[][] board;
	
	private static int compCnt = 0;	//	연결요소 번호
	
	private static int[][] compMap;	//	compMap[y][x] : (y, x)가 속한 연결요소 번호
	
	private static Set<Integer>[] graphSet;	//	graphSet[compNum] : compNum 연결요소와 인접한 연결요소 번호 목록
	private static List<Integer>[] graph;
	
	private static boolean[] usedComp;
	
	private static int answer = Integer.MAX_VALUE;

    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	N = Integer.parseInt(st.nextToken());
    	M = Integer.parseInt(st.nextToken());
    	
    	board = new int[N][M];
    	compMap = new int[N][M];
    	
    	for(int y = 0; y < N; y++) {
    		st = new StringTokenizer(br.readLine());
    		for(int x = 0; x < M; x++) {
    			board[y][x] = Integer.parseInt(st.nextToken());
    			Pos pos = new Pos(y, x);
    			startPosList.add(pos);
    		}
    	}
    	
    	Collections.sort(startPosList);
    	
    	for(int y = 0; y < N; y++) {
    		for(int x = 0; x < M; x++) {
    			if(compMap[y][x] == 0) {
    				compCnt++;
    				bfs(y, x, compCnt);
    			}
    		}
    	}
    	
    	graphSet = new HashSet[compCnt + 1];
    	graph = new ArrayList[compCnt + 1];
    	for(int num = 0; num <= compCnt; num++)
    		graphSet[num] = new HashSet<>();
    	
    	for(int y = 0; y < N; y++) {
    		for(int x = 0; x < M; x++) {
    			int cnum = compMap[y][x];
    			
    			for(int d = 0; d < 2; d++) {
    				int ny = y + dy[d];
    				int nx = x + dx[d];
    				
    				if(isIn(ny, nx) && compMap[ny][nx] != cnum) {
    					graphSet[cnum].add(compMap[ny][nx]);
    					graphSet[compMap[ny][nx]].add(cnum);
    				}
    			}
    		}
    	}
    	
    	for(int num = 0; num <= compCnt; num++)
    		graph[num] = new ArrayList<>(graphSet[num]);
    	
    	usedComp = new boolean[compCnt + 1];
    	for(int i = 0; i < startPosList.size() / 2; i++) {
    		Pos pos = startPosList.get(i);
    		int y = pos.y;
    		int x = pos.x;
    		
    		bfs(y, x);
    	}
    	
    	System.out.println(answer);
    }	//	main-end
    
    private static void bfs(int y, int x) {
    	int compNum = compMap[y][x];
    	
    	if(usedComp[compNum])
    		return;
    	
    	usedComp[compNum] = true;
    	
    	Queue<int[]> queue = new LinkedList<>();
    	boolean[] visited = new boolean[compCnt + 1];
    	int res = 0;
    	
    	
    	visited[compNum] = true;
    	queue.offer(new int[] {compNum, 0});
    	
    	while(!queue.isEmpty()) {
    		int[] cur = queue.poll();
    		int cnum = cur[0];
    		int ccost = cur[1];
    		
    		if(ccost + 1 > answer)
    			return;
    		
    		for(int nnum : graph[cnum]) {
    			if(!visited[nnum]) {
    				visited[nnum] = true;
    				queue.offer(new int[] {nnum, ccost + 1});
    				res = Math.max(res, ccost + 1);
    			}
    		}
    	}
    	
    	answer = Math.min(answer, res);
    }
    
    private static void bfs(int y, int x, int num) {
    	Queue<int[]> queue = new LinkedList<>();
    	int color = board[y][x];
    	compMap[y][x] = num;
    	queue.offer(new int[] {y, x});
    	
    	while(!queue.isEmpty()) {
    		int[] cur = queue.poll();
    		int cy = cur[0];
    		int cx = cur[1];
    		
    		for(int d = 0; d < 4; d++) {
    			int ny = cy + dy[d];
    			int nx = cx + dx[d];
    			
    			if(isIn(ny, nx) && board[ny][nx] == color && compMap[ny][nx] == 0) {
    				compMap[ny][nx] = num;
    				queue.offer(new int[] {ny, nx});
    			}
    		}
    	}
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < N) && (0 <= x && x < M);
    }
    
    private static class Pos implements Comparable<Pos> {
    	public int y;
    	public int x;
    	public int dist;	//	(N / 2, M / 2)과의 맨허튼 거리
    	
    	public Pos(int y, int x) {
    		this.y = y;
    		this.x = x;
    		this.dist = Math.abs(y - N / 2) + Math.abs(x - M / 2);
    	}

		@Override
		public int compareTo(Pos other) {
			return Integer.compare(this.dist, other.dist);
		}
    }
}	//	Main-class-end
```