# 25_08_13_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 코드트리 빵**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/codetree-mon-bread/discussion/5458)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	/**
	 * 조건 누락
	 * 이동할 수 있는 사람이 모두 이동한 후에 도착한 칸을 지나갈 수 없게 해야함
	 * 한명 이동하고 그 사람 도착했다고 바로 지나갈 수 없게 해서는 안된다.
	 * 
	 * 실수
	 * 편의점까지 최단거리로 이동하기 위해 한칸 이동할 칸 찾을때 NONE인 칸 제외하는 것 누락
	 **/
	
	private static final int BASE = 1;
	private static final int NONE = -1;	//	그 누구도 지나갈 수 없는 칸
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
		
	private static int N;	//	격자 크기 N * N
	private static int M;	//	사람/편의점 수 (최대 30)
	
	private static int[][] map;
	
	//	storePos[i] : i번째 편의점 좌표
	private static int[][] storePos;
	
	//	personArr[i] : i번째 사람
	private static Person[] personArr;

	//	자신이 원하는 편의점에 도달한 사람 수
	private static int arrival = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		storePos = new int[M + 1][2];
		personArr = new Person[M + 1];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				map[y][x] = Integer.parseInt(st.nextToken());
		}
		
		for(int i = 1; i <= M; i++) {
			st = new StringTokenizer(br.readLine());
			storePos[i][0] = Integer.parseInt(st.nextToken()) - 1;
			storePos[i][1] = Integer.parseInt(st.nextToken()) - 1;
		}
		
		for(int time = 1; ; time++) {
			//	M명의 사람 이동
			for(int nth = 1; nth <= M; nth++) {
				Person person = personArr[nth];
				
				if(person == null)	//	아직 nth번째 사람은 베이스캠프에 오지 않았을 경우
					break;
				
				int py = person.y;
				int px = person.x;
				
				//	nth번째 사람은 자신이 원하는 편의점에 도달했을 경우
				if(py == storePos[nth][0] && px == storePos[nth][1])
					continue;
				
				//	nth번째 사람이 움직임
				move(nth);
			}
			
			//	이동할 수 있는 사람 모두 이동 후에 도착한 사람 칸을 지날 수 없게 변경
			for(int nth = 1; nth <= M; nth++) {
				Person person = personArr[nth];
				
				if(person == null)	//	아직 nth번째 사람은 베이스캠프에 오지 않았을 경우
					break;
				
				int py = person.y;
				int px = person.x;
				
				//	nth번째 사람은 자신이 원하는 편의점에 도달했을 경우
				if(py == storePos[nth][0] && px == storePos[nth][1])
					map[py][px] = NONE;
			}
			
			if(time <= M) {
				int sy = storePos[time][0];
				int sx = storePos[time][1];			//	time번째 사람이 가고자 하는 편의점 위치
				int[] base = findBase(sy, sx);		//	편의점으로부터 가장 가까운 베이스 캠프 위치 찾음
				int by = base[0];
				int bx = base[1];
				
				personArr[time] = new Person(by, bx);	//	time번째 사람을 베이스 캠프에 위치시킴
				map[by][bx] = NONE;						//	누구도 지나갈 수 없음
			}
			
			if(arrival == M) {	//	모두 원하는 편의점에 도달함
				System.out.println(time);
				return;
			}
		}
	}	//	main-end
	
	private static void move(int nth) {
		Person person = personArr[nth];	//	편의점을 향해 이동하려는 사람
		int py = person.y;
		int px = person.x;				//	이동 시작할 위치
		int sy = storePos[nth][0];
		int sx = storePos[nth][1];		//	이동하고자 하는 편의점 위치
	
		int resD = INF;
		int resY = INF;
		int resX = INF;
		
		for(int d = 0; d < 4; d++) {
			int ny = py + dy[d];
			int nx = px + dx[d];
			
			if(isIn(ny, nx) && map[ny][nx] != NONE) {
				int dist = bfs(ny, nx, sy, sx);
				
				if(dist < resD) {
					resD = dist;
					resY = ny;
					resX = nx;
				}
			}
		}
		
		if(resY == sy && resX == sx)
			arrival++;
		
		person.y = resY;
		person.x = resX;
	}
	
	private static int bfs(int curY, int curX, int sy, int sx) {
		if(curY == sy && curX == sx)
			return 0;
		
		Queue<int[]> queue = new LinkedList<>();
		int[][] dist = new int[N][N];
		
		for(int y = 0; y < N; y++)
			Arrays.fill(dist[y], INF);
		
		dist[curY][curX] = 0;
		queue.offer(new int[] {curY, curX, dist[curY][curX]});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cd = cur[2];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nd = cd + 1;
				
				if(isIn(ny, nx) && map[ny][nx] != NONE && dist[ny][nx] == INF) {
					dist[ny][nx] = nd;
					
					if(ny == sy && nx == sx)
						return dist[ny][nx];
					
					queue.offer(new int[] {ny, nx, dist[ny][nx]});
				}
			}
		}
		
		return INF;
	}
	
	//	편의점 (sy, sx)에서 가장 가까운 베이스캠프 위치
	private static int[] findBase(int sy, int sx) {
		Queue<int[]> queue = new LinkedList<>();
		int[][] dist = new int[N][N];
		int resD = INF;	//	편의점에서 가장 가까운 베이스캠프 거리
		int resY = INF;
		int resX = INF;	//	그 베이스캠프의 좌표
		
		for(int y = 0; y < N; y++)
			Arrays.fill(dist[y], INF);
		
		dist[sy][sx] = 0;
		queue.offer(new int[] {sy, sx, dist[sy][sx]});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cd = cur[2];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nd = cd + 1;
				
				if(isIn(ny, nx) && map[ny][nx] != NONE && dist[ny][nx] == INF) {
					dist[ny][nx] = nd;
					queue.offer(new int[] {ny, nx, dist[ny][nx]});
					
					if(map[ny][nx] == BASE) {
						if(dist[ny][nx] < resD) {
							resD = dist[ny][nx];
							resY = ny;
							resX = nx;
						}
						else if(dist[ny][nx] == resD) {
							if(ny < resY) {
								resY = ny;
								resX = nx;
							}
							else if(ny == resY) {
								if(nx < resX)
									resX = nx;
							}
								
						}
					}
				}
			}
		}
		
		return new int[] {resY, resX};
	}
	
	//	위, 왼쪽, 오른쪽, 아래쪽 순서
	private static final int[] dy = {-1, 0, 0, 1};
	private static final int[] dx = {0, -1, 1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
	
	private static class Person {
		public int y;
		public int x;
		
		public Person(int y, int x) {
			this.y = y;
			this.x = x;
		}
	}
}	//	Main-class-end
```