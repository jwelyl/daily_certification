# 25_08_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2842 집배원 한상덕

[2842번: 집배원 한상덕](http://boj.ma/2842/t)

**Parametric Search**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_H = 1_000_000;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static char[][] map;
    private static int[][] heights;
    
    private static int sy = 0;
    private static int sx = 0;	//	우체국 위치
    private static int cnt = 0;	//	배달해야 하는 집 개수
    
    private static final boolean[] heightUsed = new boolean[MAX_H];
    private static final List<Integer> heightList = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        map = new char[N][N];
        heights = new int[N][N];
        
        for(int y = 0; y < N; y++) {
        	map[y] = br.readLine().toCharArray();
        	for(int x = 0; x < N; x++) {
        		if(map[y][x] == 'P') {
        			sy = y;
        			sx = x;
        		}
        		else if(map[y][x] == 'K')
        			cnt++;
        	}
        }
        
        for(int y = 0; y < N; y++) {
        	st = new StringTokenizer(br.readLine());
        	for(int x = 0; x < N; x++) {
        		heights[y][x] = Integer.parseInt(st.nextToken());
        		if(!heightUsed[heights[y][x]]) {
        			heightUsed[heights[y][x]] = true;
        			heightList.add(heights[y][x]);	//	고유한 높이 저장
        		}
        	}
        }
        
        Collections.sort(heightList);
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
    	int start = 0;															//	가능한 최소 피로도
    	int end = heightList.get(heightList.size() - 1) - heightList.get(0);	//	가능한 최대 피로도
    	int res = end;
    	
    	while(start <= end) {
    		int mid = (start + end) / 2;	//	mid의 피로도로 모든 집에 배달 가능한지 체크
    		boolean find = false;	//	mid의 피로도로 배달 가능하면 true
    		
    		for(int i = 0; i < heightList.size(); i++) {
    			int low = heightList.get(i);
    			int high = low + mid;			//	[low, high] 범위의 높이만 이동한다고 가정
    			
    			if(heights[sy][sx] > high)
    				continue;
    			else if(heights[sy][sx] < low)
    				break;
    			
    			if(bfs(sy, sx, low, high, cnt)) {
    				find = true;
    				break;
    			}
    		}
    		
    		if(find) {
    			res = mid;
    			end = mid - 1;		//	더 적은 피로도로 배달 가능한지 체크
    		}
    		else
    			start = mid + 1;	//	더 많은 피로도로 배달 가능한지 체크
    	}
    	
    	return res;
    }
    
    private static boolean bfs(int sy, int sx, int low, int high, int cnt) {
    	Queue<int[]> queue = new LinkedList<>();
    	boolean[][] visited = new boolean[N][N];
    	
    	visited[sy][sx] = true;
    	queue.offer(new int[] {sy, sx});
    	
    	while(!queue.isEmpty()) {
    		int[] cur = queue.poll();
    		int cy = cur[0];
    		int cx = cur[1];
    		
    		for(int d = 0; d < 8; d++) {
    			int ny = cy + dy[d];
    			int nx = cx + dx[d];
    			
    			if(isIn(ny, nx) && (low <= heights[ny][nx] && heights[ny][nx] <= high) && !visited[ny][nx]) {
    				visited[ny][nx] = true;
    				queue.offer(new int[] {ny, nx});
    				
    				if(map[ny][nx] == 'K') {
    					cnt--;
    					if(cnt == 0)
    						return true;
    				}
    			}
    		}
    	}
    	
    	return false;
    }
    
    private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
    private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < N) && (0 <= x && x < N);
    }
}    //    Main-class-end
```

**Two-Pointer**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_H = 1_000_000;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static char[][] map;
    private static int[][] heights;
    
    private static int sy = 0;
    private static int sx = 0;	//	우체국 위치
    private static int cnt = 0;	//	배달해야 하는 집 개수
    
    private static final boolean[] heightUsed = new boolean[MAX_H];
    private static final List<Integer> heightList = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        map = new char[N][N];
        heights = new int[N][N];
        
        for(int y = 0; y < N; y++) {
        	map[y] = br.readLine().toCharArray();
        	for(int x = 0; x < N; x++) {
        		if(map[y][x] == 'P') {
        			sy = y;
        			sx = x;
        		}
        		else if(map[y][x] == 'K')
        			cnt++;
        	}
        }
        
        for(int y = 0; y < N; y++) {
        	st = new StringTokenizer(br.readLine());
        	for(int x = 0; x < N; x++) {
        		heights[y][x] = Integer.parseInt(st.nextToken());
        		if(!heightUsed[heights[y][x]]) {
        			heightUsed[heights[y][x]] = true;
        			heightList.add(heights[y][x]);	//	고유한 높이 저장
        		}
        	}
        }
        
        Collections.sort(heightList);
        
        System.out.println(twoPointers());
    }    //    main-end
    
    private static int twoPointers() {
    	int start = 0;
    	int end = 0;
    	int res = heightList.get(heightList.size() - 1);
    	
    	int sh = heights[sy][sx];
    	
    	while(start <= end && end < heightList.size()) {
    		int low = heightList.get(start);
    		int high = heightList.get(end);		//	이동 가능한 높이 범위 설정
    		
    		if(high < sh) {		//	출발 지점이 이동 가능한 최고 높이보다 높을 경우
    			end++;
    			continue;
    		}
    		else if(sh < low)	//	출발 지점이 이동 가능한 최저 높이보다 낮을 경우
    			break;
    		
    		if(bfs(sy, sx, low, high, cnt))	{		//	[low, high] 범위에서 이동 가능할 경우
    			res = Math.min(res, high - low);	//	최소 피로도 갱신
    			start++;							//	피로도 더 줄여보기
    		}
    		else									//	[low, high] 범위에서 이동이 불가능할 경우
    			end++;								//	피로도 더 늘려보기
    	}
    	
    	return res;
    }
    
    private static boolean bfs(int sy, int sx, int low, int high, int cnt) {
    	Queue<int[]> queue = new LinkedList<>();
    	boolean[][] visited = new boolean[N][N];
    	
    	visited[sy][sx] = true;
    	queue.offer(new int[] {sy, sx});
    	
    	while(!queue.isEmpty()) {
    		int[] cur = queue.poll();
    		int cy = cur[0];
    		int cx = cur[1];
    		
    		for(int d = 0; d < 8; d++) {
    			int ny = cy + dy[d];
    			int nx = cx + dx[d];
    			
    			if(isIn(ny, nx) && (low <= heights[ny][nx] && heights[ny][nx] <= high) && !visited[ny][nx]) {
    				visited[ny][nx] = true;
    				queue.offer(new int[] {ny, nx});
    				
    				if(map[ny][nx] == 'K') {
    					cnt--;
    					if(cnt == 0)
    						return true;
    				}
    			}
    		}
    	}
    	
    	return false;
    }
    
    private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
    private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < N) && (0 <= x && x < N);
    }
}    //    Main-class-end
```