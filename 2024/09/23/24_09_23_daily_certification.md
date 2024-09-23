# 24_09_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 19238 스타트 택시

[](https://www.acmicpc.net/problem/19238)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.Arrays;
import java.util.LinkedList;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	private static final int WALL = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	맵 크기
	private static int M;	//	손님 수
	private static int F;	//	현재 보유 중인 연료
	
	private static int[][] map;	//	맵
	
	private static int taxiY;
	private static int taxiX;	//	택시 위치
	
	private static int[][] passengers;	//	승객 정보, passengers[num] : num번째 승객 정보 {출발 y좌표, 출발 x좌표, 도착 y좌표, 도착 x좌표}
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken()); 
		F = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		passengers = new int[M + 1][5];
		
		for(int r = 0; r < N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < N; c++) {
				map[r][c] = Integer.parseInt(st.nextToken());
				if(map[r][c] == 1)	//	벽일 경우
					map[r][c] = WALL;
			}
		}
		
		st = new StringTokenizer(br.readLine());
		taxiY = Integer.parseInt(st.nextToken()) - 1;
		taxiX = Integer.parseInt(st.nextToken()) - 1;	//	택시 초기 위치 입력
		
		for(int m = 1; m <= M; m++) {	//	m명 손님 정보 입력
			st = new StringTokenizer(br.readLine());
			int fy = Integer.parseInt(st.nextToken()) - 1;
			int fx = Integer.parseInt(st.nextToken()) - 1;	//	m번째 손님 출발 지점
			int ty = Integer.parseInt(st.nextToken()) - 1;
			int tx = Integer.parseInt(st.nextToken()) - 1;	//	m번째 손님 도착 지점
			
			map[fy][fx] = m;	//	(fy, fx)칸에 m번째 손님 배치
			
			passengers[m][0] = fy;
			passengers[m][1] = fx;	//	손님 출발점
			passengers[m][2] = ty;
			passengers[m][3] = tx;	//	손님 도착점
		}
		
		boolean ok = true;
		for(int i = 0; i < M; i++) {	//	M명의 손님 모두 목적지로 이동시켜야 함
			int[] step1 = bfs1();
			int p = step1[0];	//	태울 손님
			int f1 = step1[1];	//	손님한테 가는데 필요한 연료
			
			if(F < f1) {	//	손님한테 갈 수 없을 경우
				ok = false;
				break;
			}
			
			F -= f1;			//	해당 승객 태우러 가면서 연료 소모
			
			taxiY = passengers[p][0];
			taxiX = passengers[p][1];	//	해당 승객의 위치에 택시 위치
			
			int f2 = bfs2(passengers[p][2], passengers[p][3]);	//	해당 승객을 목적지까지 데려다 주는데 필요한 연료
			
			if(F < f2) {	//	손님을 목적지까지 데려다 줄 수 없는 경우
				ok = false;
				break;
			}
			
			F -= f2;		//	해당 승객 목적지에 데려다 주면서 연료 소모
			F += (f2 * 2);	//	해당 승객 데려다 준 후 소모한 연료의 2배 충전
			
			taxiY = passengers[p][2];
			taxiX = passengers[p][3];	//	해당 승객의 목적지에 택시 위치
			
			map[passengers[p][0]][passengers[p][1]] = 0;	//	처리한 손님 지도에서 제거
		}
		
		System.out.println(ok ? F : -1);
	}	//	main-end
	
	//	(taxiY, taxiX)에서 가장 가까운 손님 찾기
	//	가장 가까운 거리의 손님이 여러명이면, 그 중에서 행번호가 가장 작은 손님 찾기
	//	그런 손님도 여러명일 경우, 그 중에서 열번호가 가장 작은 손님 찾기
	//	{손님 번호, 필요한 연료량} 반환
	private static int[] bfs1() {
		int minDist = INF;
		int minY = INF;
		int minX = INF;
		int minDistP = INF;
		
		if(map[taxiY][taxiX] >= 1) {	//	택시 시작 위치에 택시 존재할 경우
			minDist = 0;
			minY = taxiY;
			minX = taxiX;
			minDistP = map[taxiY][taxiX];
			
			return new int[] {minDistP, minDist};
		}
		
		Queue<int[]> q = new LinkedList<>();
		
		int[][] dist = new int[N][N];
		for(int r = 0; r < N; r++)
			Arrays.fill(dist[r], INF);
		
		dist[taxiY][taxiX] = 0;
		q.offer(new int[] {taxiY, taxiX, dist[taxiY][taxiX]});
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];		//	현재 점
			int cdist = cur[2];		//	현재 점까지의 거리
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	다음 점
				int ndist = cdist + 1;	//	다음 점까지의 거리
				
				//	다음 점이 범위 내고, 벽이 아니며, 처음 방문하는 경우
				if(isIn(ny, nx) && map[ny][nx] != WALL && dist[ny][nx] == INF) {
					dist[ny][nx] = ndist;
					q.offer(new int[] {ny, nx, ndist});
					
					if(map[ny][nx] >= 1) {	//	손님일 경우
						if(dist[ny][nx] < minDist) {
							minDist = dist[ny][nx];
							minY = ny;
							minX = nx;
							minDistP = map[ny][nx];
						}
						else if(dist[ny][nx] == minDist) {
							if(ny < minY) {
								minY = ny;
								minX = nx;
								minDistP = map[ny][nx];
							}
							else if(ny == minY && nx < minX) {
								minX = nx;
								minDistP = map[ny][nx];
							}
						}
					}
				}
			}
		}	//	while-end
		
		return new int[] {minDistP, minDist};
	}
	
	//	(taxiY, taxiX)에서 (ty, tx)까지의 최단거리 반환
	private static int bfs2(int ty, int tx) {
		Queue<int[]> q = new LinkedList<>();
		
		int[][] dist = new int[N][N];
		for(int r = 0; r < N; r++)
			Arrays.fill(dist[r], INF);
		
		dist[taxiY][taxiX] = 0;
		q.offer(new int[] {taxiY, taxiX, dist[taxiY][taxiX]});
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];		//	현재 점
			int cdist = cur[2];		//	현재 점까지의 거리
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	다음 점
				int ndist = cdist + 1;	//	다음 점까지의 거리
				
				//	다음 점이 범위 내고, 벽이 아니며, 처음 방문하는 경우
				if(isIn(ny, nx) && map[ny][nx] != WALL && dist[ny][nx] == INF) {
					dist[ny][nx] = ndist;
					q.offer(new int[] {ny, nx, ndist});
				}
			}
		}	//	while-end
		
		
		return dist[ty][tx];
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```

### BOJ 20366 같이 눈사람 만들래?

[](https://www.acmicpc.net/problem/20366)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N; // 눈덩이 개수
	private static int[] heights; // 눈덩이 높이

	private static int minDiff = Integer.MAX_VALUE; // 두 눈사람 높이차 최소

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		heights = new int[N];
		st = new StringTokenizer(br.readLine());
		for (int i = 0; i < N; i++)
			heights[i] = Integer.parseInt(st.nextToken());

		Arrays.sort(heights); // 눈덩이 높이 오름차순 정렬

		for (int h1 = 0; h1 <= N - 4; h1++) { // 엘사가 만든 눈사람 머리 위치
			for (int b1 = h1 + 3; b1 < N; b1++) { // 엘사가 만든 눈사람 몸통 위치 (h1, b1 사이에 눈덩이 2개는 존재해야 함)
				int elsa = heights[h1] + heights[b1]; // 엘사가 만든 눈사람 높이

				int h2 = h1 + 1; // 안나가 만든 눈사람 머리 위치
				int b2 = b1 - 1; // 안나가 만든 눈사람 몸통 위치

				while (h2 < b2) {
					int anna = heights[h2] + heights[b2];
					minDiff = Math.min(minDiff, Math.abs(anna - elsa));

					if (minDiff == 0) { // 높이 차이가 0일 경우 더 확인할 필요 없음
						System.out.println(0);
						return;
					}

					if (anna < elsa) // 안나가 만든 눈사람이 더 작을 경우
						h2++; // 안나가 만든 눈사람 머리 크기 키워서 높이 차이 줄이기
					else // 안나가 만든 눈사람이 더 클 경우
						b2--; // 안나가 만든 눈사람 몸통 크기 줄여서 높이 차이 줄이기
				}
			}
		}

		System.out.println(minDiff);
	} // main-end
} // Main-class-end
```