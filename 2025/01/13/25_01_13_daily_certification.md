# 25_01_13_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 17837 새로운 게임 2**

[17837번: 새로운 게임 2](https://boj.ma/17837/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int WHITE = 0;
	private static final int BLUE = 2;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int K;
	
	private static int[][] colors;			//	colors[y][x] : (y, x) 칸의 색
	private static List<Integer>[][] map;	//	map[y][x] : (y, x) 칸에 있는 말의 번호들, index = 0에 있는 말이 가장 아래에 있는 말임
	private static int[][] pos;				//	pos[k] : k번째 말의 현재 좌표 {y, x}
	private static int[] dirs;				//	dirs[k] : k번째 말의 현재 이동방향
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		colors = new int[N][N];
		map = new ArrayList[N][N];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				colors[y][x] = Integer.parseInt(st.nextToken());
				map[y][x] = new ArrayList<>();
			}
		}
		
		pos = new int[K + 1][2];
		dirs = new int[K + 1];
		
		for(int k = 1; k <= K; k++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			int dir = Integer.parseInt(st.nextToken()) - 1;
			
			pos[k][0] = y;
			pos[k][1] = x;
			dirs[k] = dir;
			
			map[y][x].add(k);
		}
		
		for(int turn = 1; turn <= 1_000; turn++) {
			if(moveAll()) {
				System.out.println(turn);
				return;
			}
		}
		
		System.out.println(-1);
	}	//	main-end
	
	//	1번 말부터 K번 말까지 순서대로 움직임
	private static boolean moveAll() {
		for(int k = 1; k <= K; k++) {
			if(move(k))	//	k번째 말이 움직인 결과 한칸에 말이 4마리 이상이 된 경우
				return true;
		}
		
		return false;
	}
	
	//	nth번째 말이 움직임
	private static boolean move(int nth) {
		int cy = pos[nth][0];
		int cx = pos[nth][1];	//	nth번째 말 현재 위치
		int cdir = dirs[nth];	//	nth번째 말 현재 이동방향
		
		int ny = cy + dy[cdir];
		int nx = cx + dx[cdir];	//	nth번째 말 현재 이동하려는 칸
		
		if(!isIn(ny, nx) || colors[ny][nx] == BLUE)	//	이동하려는 칸이 범위 밖이거나 파란색일 경우
			return moveBlue(nth, cy, cx, ny, nx);
		else if(colors[ny][nx] == WHITE)			//	이동하려는 칸이 흰색일 경우
			return moveWhite(nth, cy, cx, ny, nx);
		else										//	이동하려는 칸이 빨간색일 경우
			return moveRed(nth, cy, cx, ny, nx);
 	}
	
	//	nth번째 말이 (cy, cx)에서 이동하려는 칸 (ny, nx)가 범위 밖이거나 파란색 칸일 경우
	private static boolean moveBlue(int nth, int cy, int cx, int ny, int nx) {
		int rdir = (dirs[nth] == 0 ? 1 : (dirs[nth] == 1 ? 0 : (dirs[nth] == 2 ? 3 : 2)));	//	역방향
		dirs[nth] = (dirs[nth] == 0 ? 1 : (dirs[nth] == 1 ? 0 : (dirs[nth] == 2 ? 3 : 2)));	//	역방향
		ny = cy + dy[dirs[nth]];
		nx = cx + dx[dirs[nth]];	//	가려는 칸 다시 찾기
		
		if(!isIn(ny, nx) || colors[ny][nx] == BLUE)	//	이동하려는 칸이 그래도 범위 밖이거나 파란색일 경우
			return false;
		
		if(colors[ny][nx] == WHITE)
			return moveWhite(nth, cy, cx, ny, nx);
		else
			return moveRed(nth, cy, cx, ny, nx);
	}
	
	//	nth번째 말이 (cy, cx)에서 이동하려는 칸 (ny, nx)가 흰색일 경우
	private static boolean moveWhite(int nth, int cy, int cx, int ny, int nx) {
		List<Integer> from = map[cy][cx];
		List<Integer> to = map[ny][nx];
		List<Integer> tmp = new ArrayList<>();	//	nth번째 말과 그 위에 있는 말들 임시 저장
		
		int size = from.size();
		
		for(int i = size - 1; i >= 0; i--) {
			int num = from.get(i);
			
			pos[num][0] = ny;
			pos[num][1] = nx;	//	이동할 말들 위치 조정
			
			tmp.add(num);
			from.remove(i);
			if(num == nth)
				break;
		}
		
		size = tmp.size();
		for(int i = size - 1; i >= 0; i--) {
			int num = tmp.get(i);
			
			tmp.remove(i);
			to.add(num);
		}
		
		return map[ny][nx].size() >= 4;
	}
	
	//	nth번째 말이 (cy, cx)에서 이동하려는 칸 (ny, nx)가 빨간색일 경우
	private static boolean moveRed(int nth, int cy, int cx, int ny, int nx) {
		List<Integer> from = map[cy][cx];
		List<Integer> to = map[ny][nx];
		List<Integer> tmp = new ArrayList<>();	//	nth번째 말과 그 위에 있는 말들 임시 저장
		
		int size = from.size();
		for(int i = size - 1; i >= 0; i--) {
			int num = from.get(i);
			
			pos[num][0] = ny;
			pos[num][1] = nx;	//	이동할 말들 위치 조정
			
			tmp.add(num);
			from.remove(i);
			if(num == nth)
				break;
		}
		
		size = tmp.size();
		for(int i = 0; i < size; i++) {
			int num = tmp.get(i);
			to.add(num);
		}
		
		return map[ny][nx].size() >= 4;
	}
	
	private static final int[] dy = {0, 0, -1, 1};
	private static final int[] dx = {1, -1, 0, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```

### **BOJ 1162 도로포장**

[1162번: 도로포장](https://boj.ma/1162/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
    private static final long INF = 50_000L * 1_000_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    도시 개수
    private static int M;    //    도로 개수
    private static int K;    //    포장할 수 있는 도로 개수
    
    private static List<int[]>[] graph;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int v = 1; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new int[] {v2, c});
            graph[v2].add(new int[] {v1, c});
        }
        
        System.out.println(dijkstra());
    }    //    main-end
    
    private static long dijkstra() {
        PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
        long[][] dist = new long[K + 1][N + 1];
        long res = INF;
        
        for(int k = 0; k <= K; k++)
        	Arrays.fill(dist[k], INF);
        
        dist[0][1] = 0;
        pq.offer(new long[] {1, dist[0][1], 0});
        
        while(!pq.isEmpty()) {
            long[] cur = pq.poll();
            int cv = (int)cur[0];		//	cv까지 오는데	
            long cc = cur[1];			//	cc의 비용으로 옴
            int ccnt = (int)cur[2];		//	ccnt개의 도로를 포장해서
            
            if(dist[ccnt][cv] < cc)
                continue;
            
            for(int[] edge : graph[cv]) {
                int nv = edge[0];
                long nc = cc;
                int ncnt = ccnt;
                
                //	edge를 포장하지 않는 경우
                nc = cc + edge[1];
                
                if(nc < dist[ncnt][nv]) {
                	dist[ncnt][nv] = nc;
                	pq.offer(new long[] {nv, dist[ncnt][nv], ncnt});
                }
                
                //	edge를 포장할 수 있는 경우
                if(ccnt < K) {
                	ncnt++;
                	nc = cc;
                	
                	if(nc < dist[ncnt][nv]) {
                    	dist[ncnt][nv] = nc;
                    	pq.offer(new long[] {nv, dist[ncnt][nv], ncnt});
                    }
                }
            }
        }
       
        for(int k = 0; k <= K; k++)
        	res = Math.min(res, dist[k][N]);
    
        return res;
    }
}    //    Main-class-end
```