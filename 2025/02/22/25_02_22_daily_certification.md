# 25_02_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23634 **미안하다 이거 보여주려고 어그로 끌었다**

[23634번: 미안하다 이거 보여주려고 어그로 끌었다](http://boj.ma/23634/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static int totalSize = 0;			//	분리집합 크기 합
	private static int prevNumOfDs = 0;			//	이전 분리집합 개수
	private static int curNumOfDs = 0;			//	현재 분리집합 개수
	private static int lastChangedTime = 0;		//	마지막으로 분리집합 수가 변경된 시간
	private static int lastChangedSize = 0;		//	그때 변경된 분리집합 크기 합
	
	private static char[][] map;
	private static int[][] dsMap;
	private static final Queue<int[]> queue = new LinkedList<>();		//	최초 분리집합 식별 결과 큐, 후에 분리집합들 합쳐질때 bfs에 사용할 큐
	
	private static int[] parents;
	
	private static final List<Integer> dsList = new ArrayList<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		dsMap = new int[N][M];
		
		parents = new int[N * M + 1];
		
		for(int y = 0; y < N; y++)
			map[y] = br.readLine().toCharArray();
	
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++) {
				if(map[y][x] == '0' && dsMap[y][x] == NONE) {
					init(y, x, M * y + x + 1);
					dsList.add(M * y + x + 1);
					curNumOfDs++;
				}
			}
		}
		
		prevNumOfDs = curNumOfDs;
		lastChangedSize = totalSize;
		
		if(curNumOfDs == 0)
			System.out.println("0 0");
		else
			bfs();
	}	//	main-end
	
	private static void bfs() {
		int time = 1;
		
		for(int ds : dsList)
			parents[ds] = ds;
		
		while(!queue.isEmpty()) {
			int size = queue.size();
			
			for(int i = 0; i < size; i++) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];
				int cds = cur[2];
				
				for(int d = 0; d < 4; d++) {
					int ny = cy + dy[d];
					int nx = cx + dx[d];
					
					if(isIn(ny, nx) && map[ny][nx] == '1' && dsMap[ny][nx] == NONE) {
						dsMap[ny][nx] = cds;
						totalSize++;
						queue.offer(new int[] {ny, nx, cds});
						
						for(int nd = 0; nd < 4; nd++) {
							int nny = ny + dy[nd];
							int nnx = nx + dx[nd];
							
							if(isIn(nny, nnx) && dsMap[nny][nnx] != NONE)
								union(cds, dsMap[nny][nnx]);
						}
					}
				}
			}
			
			if(prevNumOfDs != curNumOfDs) {	//	분리집합이 합쳐져서 개수가 변한 경우
				lastChangedTime = time;			//	변한 시각
				lastChangedSize = totalSize;	//	변한 시각에서의 분리집합 크기 합
				prevNumOfDs = curNumOfDs;
			}
			
			time++;
		}
		
		System.out.println(lastChangedTime + " " + lastChangedSize);
	}
	
	private static void init(int y, int x, int dsNum) {
		Queue<int[]> q = new LinkedList<>();
		q.offer(new int[] {y, x});
		queue.offer(new int[] {y, x, dsNum});
		totalSize++;
		dsMap[y][x] = dsNum;
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0;d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && map[ny][nx] == '0' && dsMap[ny][nx] == NONE) {
					q.offer(new int[] {ny, nx});
					queue.offer(new int[] {ny, nx, dsNum});
					totalSize++;
					dsMap[ny][nx] = dsNum;
				}
			}
		}
	}

	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	private static int findDs(int v) {
		if(v == parents[v])
			return v;
		
		return parents[v] = findDs(parents[v]);
	}
	
	private static void union(int v1, int v2) {
		v1 = findDs(v1);
		v2 = findDs(v2);
		
		if(v1 != v2) {		//	두 분리집합이 서로 같지 않은 경우
			curNumOfDs--;	//	두 분리집합이 합쳐지면서 분리집합 개수 1 감소
			
			if(v1 < v2)
				parents[v2] = v1;
			else
				parents[v1] = v2;
		}
	}
}	//	Main-class-end
```