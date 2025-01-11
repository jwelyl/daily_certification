# 25_01_11_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 19237 어른 상어**

[19237번: 어른 상어](https://boj.ma/19237/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_TIME = 1_000;
	private static final int INVALID = -1;
	private static final int NONE = 0;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	격자 크기 N * N
	private static int M;	//	최초 상어 수
	private static int K;	//	냄새 지속시간
	
	private static int[][] sharkMap;				//	sharkMap[y][x] : (y, x)에 존재하는 상어 번호, 없으면 NONE
	private static int[][][] smellMap;				//	smellMap[y][x] : (y, x)에 존재하는 {냄새를 남긴 상어 번호, 냄새가 사라질 시간}
	
	private static int[][] sharkPos;				//	sharkPos[num] : num번째 상어의 {y좌표, x좌표}
	private static int[] sharkDirs;					//	sharkDirs[num] : num번째 상어의 현재 방향
	private static int[][][] sharkPriorities;		//	sharkPriorities[num][dir] : num번째 상어의 현재 방향이 dir일때 이동 우선순위
	
	private static int time = 0;	//	전역 시간
	private static int remain = 0;	//	남은 상어 개수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		sharkMap = new int[N][N];
		smellMap = new int[N][N][2];
		
		sharkPos = new int[M + 1][2];
		sharkDirs = new int[M + 1];
		sharkPriorities = new int[M + 1][4][4];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				int num = Integer.parseInt(st.nextToken());
				sharkMap[y][x] = num;
				if(num != NONE) {
					sharkPos[num][0] = y;
					sharkPos[num][1] = x;
				}
			}
		}
		
		st = new StringTokenizer(br.readLine());
		for(int num = 1; num <= M; num++)
			sharkDirs[num] = Integer.parseInt(st.nextToken()) - 1;
		
		for(int num = 1; num <= M; num++) {
			for(int dir = 0; dir < 4; dir++) {
				st = new StringTokenizer(br.readLine());
				for(int d = 0; d < 4; d++)
					sharkPriorities[num][dir][d] = Integer.parseInt(st.nextToken()) - 1;
			}
		}
		
		remain = M;
		
		while(time < MAX_TIME) {
			smell();
			move();
			time++;
			
			if(remain == 1) {	//	1번 상어만 남았을 경우
				System.out.println(time);
				return;
			}
		}
		
		System.out.println(-1);	//	1,000초가 넘어도 다른 상어가 있을 경우
	}	//	main-end
	
	//	time초에 1 ~ M번 상어 중 살아있는 상어들이 자기 자리에 냄새를 남김
	private static void smell() {
		for(int num = 1; num <= M; num++) {
			if(isDead(num))	//	num번째 상어가 죽은 경우 skip
				continue;
			
			int y = sharkPos[num][0];
			int x = sharkPos[num][1];
			smellMap[y][x][0] = num;			//	(y, x)에 num 상어가 냄새를 남김
			smellMap[y][x][1] = time + K; 		//	해당 냄새를 time초에 남겨서 time + K초에 사라짐
		}
	}
	
	private static void move() {
		int[][] tmpSharkMap = new int[N][N];	//	동시 이동을 위한 임시 맵
		
		for(int num = 1; num <= M; num++) {	//	작은 번호부터 이동해보기
			if(isDead(num))	//	num번째 상어는 이미 죽은 경우
				continue;
			
			int cdir = sharkDirs[num];	//	num번째 상어의 현재 방향
			int cy = sharkPos[num][0];
			int cx = sharkPos[num][1];	//	num번째 상어 현재 위치
			
			int nextY = INVALID;
			int nextX = INVALID;		//	num번째 상어가 이동할 칸
			int nextDir = INVALID;		//	num번째 상어가 이동할 방향
			int[] cpriorities = sharkPriorities[num][cdir];
			
			//	먼저 인접하고 냄새 없는 칸 찾아보기
			for(int d = 0; d < 4; d++) {
				int ndir = cpriorities[d];
				int ny = cy + dy[ndir];
				int nx = cx + dx[ndir];
				
				//	(ny, nx)가 인접하며 아무 냄새도 없을 경우
				if(isIn(ny, nx) && !isSmell(ny, nx)) {
					nextY = ny;
					nextX = nx;			//	(ny, nx)로 이동하면 된다.
					nextDir = ndir;		//	ndir 방향으로 이동하면 된다.
					break;
				}
			}
			
			//	인접한 칸 중에 냄새 없는 칸이 없을 경우, 자기 냄새 있는 칸으로 감
			if(nextY == INVALID && nextX == INVALID) {
				for(int d = 0; d < 4; d++) {
					int ndir = cpriorities[d];
					int ny = cy + dy[ndir];
					int nx = cx + dx[ndir];
					
					//	(ny, nx)가 인접하며 자기 냄새가 있을 경우
					if(isIn(ny, nx) && smellMap[ny][nx][0] == num) {
						nextY = ny;
						nextX = nx;	//	(ny, nx)로 이동하면 된다.
						nextDir = ndir;		//	ndir 방향으로 이동하면 된다.
						break;
					}
				}
			}
			
			if(tmpSharkMap[nextY][nextX] != NONE) {	//	이미 (nextY, nextX)에 더 번호가 작은 상어가 먼저 와 있을 경우
				sharkPos[num][0] = INVALID;
				sharkPos[num][1] = INVALID;
				sharkDirs[num] = INVALID;	//	num번째 상어는 죽음
				remain--;					//	남은 상어 수 1 감소
			}
			else {	//	(nextY, nextX)를 차지할 수 있을 경우
				tmpSharkMap[nextY][nextX] = num;
				sharkPos[num][0] = nextY;
				sharkPos[num][1] = nextX;	//	num번째 상어는 (nextY, nextX)를 차지함, 추후 더 번호 큰 상어가 오면 죽임
				sharkDirs[num] = nextDir;	//	num번째 상어의 이동방향은 nextDir임
			}
		}
		
		sharkMap = tmpSharkMap;
	}
	
	//	(y, x)칸에 냄새가 남아있는지 확인
	private static boolean isSmell(int y, int x) {
		//	(y, x)칸에 냄새 남긴 상어가 있고 그 냄새가 아직 안사라졌을경우
		if(smellMap[y][x][0] != NONE && time < smellMap[y][x][1])
			return true;
		
		return false;
	}
	
	//	num번째 상어가 죽었는지 확인
	private static boolean isDead(int num) {
		return sharkPos[num][0] == INVALID && sharkPos[num][1] == INVALID && sharkDirs[num] == INVALID;
	}
	
	private static final int[] dy = {-1, 1, 0, 0};
	private static final int[] dx = {0, 0, -1, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```