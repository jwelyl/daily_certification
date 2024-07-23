# 24_07_23_daily_certification

# Problem Solving (Algorithm & SQL)

### 삼성전자 2021년 하반기 오후 1번 팩맨 (Fail → Success)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/pacman/description?page=1&pageSize=20&statuses=In+Progress)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 4;
	
	private static final int ALIVE = 0;		//	몬스터 활성 상태
	private static final int EGG   = 1;		//	몬스터 알 상태
	private static final int DEAD  = 2;		//	몬스터 시체 상태
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static final List<Monster>[][] aliveMap = new ArrayList[MAX][MAX];	//	aliveMap[i][j] : (i, j) 칸에 존재하는 활성 상태 몬스터 리스트
	private static final List<Monster>[][] eggMap = new ArrayList[MAX][MAX];	//	eggMap[i][j] : (i, j) 칸에 존재하는 알 상태 몬스터 리스트
	private static final List<Monster>[][] deadMap = new ArrayList[MAX][MAX];	//	deadMap[i][j] : (i, j) 칸에 존재하는 시체 상태 몬스터 리스트
	
	private static int time = 0;	//	전역 시간, 1턴 당 1 시간 흐름
	
	private static int M;	//	최초 몬스터 수
	private static int T;	//	턴 수
	
	private static final Packman packman = new Packman(0, 0);	//	팩맨
	
	public static void main(String[] args) throws IOException {
		init();
		
		tokens = new StringTokenizer(br.readLine());
		M = Integer.parseInt(tokens.nextToken());
		T = Integer.parseInt(tokens.nextToken());
		
		tokens = new StringTokenizer(br.readLine());
		
		packman.y = Integer.parseInt(tokens.nextToken()) - 1;
		packman.x = Integer.parseInt(tokens.nextToken()) - 1;	//	팩맨 초기 위치 입력
		
		for(int i = 0; i < M; i++) {
			tokens = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(tokens.nextToken()) - 1;
			int x = Integer.parseInt(tokens.nextToken()) - 1;	//	몬스터 초기 위치
			int dir = Integer.parseInt(tokens.nextToken());		//	몬스터 초기 방향
			
			Monster m = new Monster(dir);	//	초기 몬스터는 활성 상태
			aliveMap[y][x].add(m);	//	활성 상태 몬스터를 활성 상태 (y, x) 칸에 넣기 
		}
		
		while(time < T) {
			simulation();
			time++;
		}
		
		System.out.println(aliveCnt());
	}	//	main-end
	
	//	alive, egg, dead map 초기화
	private static void init() {
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++) {
				aliveMap[r][c] = new ArrayList<>();
				eggMap[r][c] = new ArrayList<>();
				deadMap[r][c] = new ArrayList<>();
			}
		}
	}
	
	private static void simulation() {
		//	1. 몬스터 복제 시도, 알 낳기
		layEggs();
		//	2. 활성 상태 몬스터 이동하기
		moveAlives();
		//	3. 팩맨 이동
		packmanMoves();
		//	4. 시체 처리
		clearDead();
		//	5. 몬스터 복제 완성, 알 낳기
		hatchEggs();
	}
	
	//	1. 몬스터 알 생성
	private static void layEggs() {
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++) {
				for(Monster parent : aliveMap[r][c]) {	//	(r, c) 칸에 활성 상태의 몬스터 각각에 대하여
					Monster child = new Monster(parent);	//	알 상태로 복제
					eggMap[r][c].add(child);	//	복제된 알을 알 상태 (r, c) 칸에 넣기
				}
			}
		}
	}
	
	//	2. 활성 상태 몬스터 이동
	private static void moveAlives() {
		List<Monster>[][] tmp = new ArrayList[MAX][MAX];	//	tmp[r][c] : (r, c)로 이동한 몬스터들 임시로 모아두는 리스트
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++)
				tmp[r][c] = new ArrayList<>();
		}
		
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++) {
				List<Monster> mList = aliveMap[r][c];	//	(r, c)칸에 존재하는 활성 상태 몬스터 리스트
				
				if(mList.isEmpty())	//	해당 칸에 활성 상태 몬스터 없을 경우
					continue;
				
				for(Monster m : mList) {
					int cy = r;
					int cx = c;		//	현재 몬스터 위치
					int cdir = m.dir;	//	현재 몬스터 방향
					
					int mdir = -1;	//	이동할 방향
					
					for(int i = 0; i < 8; i++) {
						int ny = cy + dyM[cdir];
						int nx = cx  +dxM[cdir];	//	이동할 칸
						
						//	현재 방향으로 이동할 칸이 범위 밖이거나, 팩맨이 있거나, 시체가 있으면
						if(!isIn(ny, nx) || (ny == packman.y && nx == packman.x) || !deadMap[ny][nx].isEmpty()) {
							cdir = nextDir(cdir);	//	다음 방향으로
							continue;
						}
						
						mdir = cdir;
						break;
					}
					
					if(mdir == -1)//	결국 어떤 방향으로도 이동할 수 없을 경우
						tmp[cy][cx].add(m);	//	제 자리에 그대로 있기
					else {
						int ny = cy + dyM[mdir];
						int nx = cx + dxM[mdir];	//	(ny, nx)칸으로 이동하면 됨
						m.dir = mdir;				//	몬스터가 바라보는 방향 변경
						
						tmp[ny][nx].add(m);	//	(ny, nx)칸으로 이동
					}
				}
			}
		}
		
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++)
				aliveMap[r][c] = tmp[r][c];
		}
	}
	
	//	3. 팩맨 이동
	private static void packmanMoves() {
		int cy = packman.y;
		int cx = packman.x;	//	팩맨의 현재 위치
		
		int[] py = new int[3];
		int[] px = new int[3];	//	팩맨이 이동할 세 칸 좌표
		boolean[][] visited = new boolean[MAX][MAX];
		
		int maxEat = -1;	//	팩맨이 최대로 먹을 수 있는 활성 상태 몬스터 수
		
		for(int d0 = 0; d0 < 4; d0++) {	//	첫 번째 이동 방향
			int ny0 = cy + dyP[d0];
			int nx0 = cx + dxP[d0];	//	첫 번째 이동 방향으로 이동한 칸
			
			if(!isIn(ny0, nx0))	//	범위 벗어날 경우
				continue;
			
			for(int d1 = 0; d1 < 4; d1++) {	//	두 번째 이동 방향
				int ny1 = ny0 + dyP[d1];
				int nx1 = nx0 + dxP[d1];	//	두 번째 이동 방향으로 이동한 칸
				
				if(!isIn(ny1, nx1))	//	범위 벗어날 경우
					continue;
				
				for(int d2 = 0; d2 < 4; d2++) {
					int ny2 = ny1 + dyP[d2];
					int nx2 = nx1 + dxP[d2];	//	세 번째 이동 방향으로 이동한 칸
		
					if(!isIn(ny2, nx2))
						continue;
					
					int eat = aliveMap[ny0][nx0].size();
					visited[ny0][nx0] = true;
					
						eat += aliveMap[ny1][nx1].size();

					
					if(!visited[ny2][nx2]) {
						eat += aliveMap[ny2][nx2].size();
						visited[ny2][nx2] = true;
					}
					
					if(eat > maxEat) {
						maxEat = eat;
						py[0] = ny0;
						px[0] = nx0;
						py[1] = ny1;
						px[1] = nx1;
						py[2] = ny2;
						px[2] = nx2;
					}
					
					visited[ny2][nx2] = false;
					visited[ny1][nx1] = false;
					visited[ny0][nx0] = false;
				}
			}
		}
		
		//	팩맨이 이동한 경로에 있는 활성 상태 몬스터 먹기 
		for(int i = 0; i < 3; i++) {
			int y = py[i];
			int x = px[i];	//	i번째 이동한 칸
			
			List<Monster> mList = aliveMap[y][x];
			for(Monster m : mList) {
				m.status = DEAD;		//	시체 상태로 변경
				m.expired = time + 2;	//	2턴 뒤에 제거됨
				deadMap[y][x].add(m);	//	시체 칸으로 이동
			}
			
			mList.clear();
		}
		
		packman.y = py[2];
		packman.x = px[2];	//	팩맨 위치 변경
	}
	
	//	4. 시체 상태 몬스터 제거
	private static void clearDead() {
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++) {
				List<Monster> tmp = new ArrayList<>();
				
				for(Monster m : deadMap[r][c]) {	//	(r, c) 칸에 존재하는 시체들 중에서
					if(m.expired > time)	//	아직 사라질 시간이 되지 않은 경우
						tmp.add(m);	//	보존하기
				}
				
				deadMap[r][c] = tmp;
			}
		}
	}
	
	//	5. 몬스터 알 부화
	private static void hatchEggs() {
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++) {
				for(Monster egg : eggMap[r][c]) {	//	(r, c) 칸에 알 상태의 몬스터 각각에 대하여
					egg.status = ALIVE;	//	몬스터 활성 상태로 변경
					aliveMap[r][c].add(egg);	//	부화한 몬스터를 활성 상태 (r, c) 칸에 넣기
				}
				
				eggMap[r][c].clear();	//	(r, c) 칸의 모든 알 부화시키기
			}
		}
	}
	
	//	현재 몬스터 방향 dir로 이동할 수 없을 때 다음 이동 방향
	private static int nextDir(int dir) {
		return (1 <= dir && dir <= 7) ? dir + 1 : 1;
	}
	
	private static int aliveCnt() {
		int sum = 0;
		
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++)
				sum += aliveMap[r][c].size();
		}
		
		return sum;
	}
	
	private static final int[] dyM = {0, -1, -1, 0, 1, 1, 1, 0, -1};
	private static final int[] dxM = {0, 0, -1, -1, -1, 0, 1, 1, 1};	//	몬스터 이동 방향
	
	private static final int[] dyP = {-1, 0, 1, 0};
	private static final int[] dxP = {0, -1, 0, 1};		//	팩맨 이동 방향
	
	private static boolean isIn(int y, int x) {	//	범위 벗어나는 지 체크
		return (0 <= y && y < MAX) && (0 <= x && x < MAX);
	}
	
	private static class Packman {
		public int y;
		public int x;
		
		public Packman(int y, int x) {
			this.y = y;
			this.x = x;
		}
	}
	
	private static class Monster {
		public int dir;			//	몬스터가 바라보는 방향
		public int status;		//	몬스터 상태
		public int expired;		//	몬스터 상태가 시체일 경우 사라지는 시간
		
		public Monster(int dir) {	//	게임 시작 시 주어지는 몬스터
			this.dir = dir;
			this.status = ALIVE;
		}
		
		public Monster(Monster m) {	//	몬스터 m을 복제함
			this(m.dir);	//	m의 위치와 초기 방향을 그대로 가짐
			this.status = EGG;		//	상태는 알인 상태
		}
	}
}	//	Main-class-end
```

### 삼성전자 2020년 상반기 오후 1번 승자독식 모노폴리 (Fail)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/pacman/description?page=1&pageSize=20&statuses=In+Progress)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;	//	독점한 사람 없는 경우
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int N;	//	격자 크기 N * N
	private static int M;	//	초기 플레이어 수 M
	private static int K;	//	독점하는 시간
	
	private static int remain;	//	남은 플레이어 수
	
	private static int time = 0;	//	전역 타이머
	
	private static Player[] playerList;
	
	private static PriorityQueue<Player>[][] playerMap;	//	playerMap[y][x] : (y, x) 칸에 존재하는 플레이어 모음, 번호 큰 플레이어부터 먼저 제거되도록 함
	private static int[][][] monopolyMap;				//	monopolyMap[y][x] : {(y, x) 칸을 독점한 플레이어 번호, 독점 남은 시간, 0일 경우 독점 무효} 
	
	public static void main(String[] args) throws IOException {
		init();
		simulation();
	}	//	main-end
	
	private static void init() throws IOException {
		tokens = new StringTokenizer(br.readLine());
		N = Integer.parseInt(tokens.nextToken());
		M = Integer.parseInt(tokens.nextToken());
		K = Integer.parseInt(tokens.nextToken());
		
		playerList = new Player[M + 1];
		
		playerMap = new PriorityQueue[N][N];
		for(int r = 0; r < N; r++)
			for(int c = 0; c < N; c++)
				playerMap[r][c] = new PriorityQueue<>();
	
		monopolyMap = new int[N][N][2];
		
		for(int r = 0; r < N; r++) {
			tokens = new StringTokenizer(br.readLine());
			for(int c = 0; c < N; c++) {
				int pNum = Integer.parseInt(tokens.nextToken());	//	플레이어 번호
				
				if(pNum != NONE) {
					Player player = new Player(pNum, r, c);	//	num번째 플레이어
					playerList[pNum] = player;
					playerMap[r][c].offer(player);
					monopoly(player);
				}
			}
		}
		
		tokens = new StringTokenizer(br.readLine());
		for(int pNum = 1; pNum <= M; pNum++) {
			int dir = Integer.parseInt(tokens.nextToken()) - 1;
			playerList[pNum].dir = dir;	//	플레이어 초기 바라보는 방향 설정
		}
		
		for(int pNum = 1; pNum <= M; pNum++) {
			for(int dir = 0; dir < 4; dir++) {	//	바라보는 방향이 dir일 때 우선순위
				tokens = new StringTokenizer(br.readLine());
				for(int i = 0; i < 4; i++) {	//	바라보는 방향이 dir일 때 i번째로 우선순위 높은 방향이
					int d = Integer.parseInt(tokens.nextToken()) - 1;	//	d 방향임
					playerList[pNum].priority[dir][i] = d;
				}
			}
		}
	}
	
	private static void simulation() {
		while(remain > 1 && time < 1000) {
			//	살아있는 모든 플레이어 한 칸씩 이동
			moveAll();
			//	각 칸 조사해서 한 칸에 여러 플레이어 있을 경우 가장 번호 작은 한 명만 남기기
			remove();
			//	독점 갱신
			renewMonopoly();
			//	새로 이동한 곳 독점하기
			monopoly();
			time++;
		}
		
		System.out.println(remain == 1 ? time : -1);
	}
	
	private static void remove() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				if(playerMap[r][c].size() > 1) {	//	(r, c) 칸에 2명 이상의 플레이어가 있을 경우
					while(playerMap[r][c].size() != 1) {	//	(r, c) 칸에 플레이어  1명만 남을 때까지
						Player player = playerMap[r][c].poll();	//	번호 큰 플레이어 찾기
						playerList[player.num] = null;			//	해당 플레이어 제거
						remain--;								//	플레이어 수 1 감소
					}
				}
				
				if(playerMap[r][c].size() == 1) {
					Player player = playerMap[r][c].peek();	//	(r, c) 칸에 남은 플레이어
					monopoly(player);	//	해당 칸을 독점하기
				}
			}
		}
	}
	
	private static void renewMonopoly() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				if(monopolyMap[r][c][0] != NONE) {	//	(r, c) 칸을 독점한 플레이어가 있을 경우
					monopolyMap[r][c][1]--;			//	독점 만료까지 남은 시간 1 감소
					
					if(monopolyMap[r][c][1] == 0)	//	독점 만료까지 남은 시간이 0일 경우
						monopolyMap[r][c][0] = NONE;	//	독점 없애기
				}
			}
		}
	}
	
	private static void moveAll() {
		//	temp[r][c] : (r, c)칸으로 이동할 플레이어 번호 모음
		List<Integer>[][] temp = new ArrayList[N][N];
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++)
				temp[r][c] = new ArrayList<>();
		}
		
		for(int pNum = 1; pNum <= M; pNum++) {
			Player player = playerList[pNum];	//	플레이어 이동
			
			if(player == null)	//	이미 제거된 플레이어일 경우, 이동 x
				continue;
			
			int cy = player.y;
			int cx = player.x;
			
			int newDir = move(player);	//	player가 이동할 방향 찾기
			
			player.dir = newDir;	//	해당 방향으로 이동할 것임
			
			int ny = cy + dy[player.dir];
			int nx = cx + dx[player.dir];
			player.y = ny;
			player.x = nx;
			
			temp[ny][nx].add(pNum);
		}
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				PriorityQueue<Player> pq = new PriorityQueue<>();	//	(r, c)의 pq
				
				for(int pNum : temp[r][c])	//	(r, c)칸으로 이동할 플레이어 번호
					pq.offer(playerList[pNum]);
				
				playerMap[r][c] = pq;
			}
		}
	}
	
	private static int move(Player player) {
		int res = -1;	//	최종적으로 이동할 방향
		
		int pNum = player.num;
		int cy = player.y;
		int cx = player.x;			//	이동할 플레이어 위치
		int cdir = player.dir;		//	이동할 플레이어 방향
		
		int[] cPriority = player.priority[cdir];	//	이동할 플레이어의 방향에 따른 우선순위
		
		for(int i = 0; i < 4; i++) {
			int d = cPriority[i];
			int ny = cy + dy[d];
			int nx = cx + dx[d];	//	다음 이동할 칸
			
			//	다음 칸이 격자 밖이거나, 다음 칸을 독점한 플레이어가 있을 경우 skip
			if(!isIn(ny, nx) || monopolyMap[ny][nx][0] != NONE)
				continue;
			
			res = d;
			break;
		}
		
		if(res == -1) {	//	주변 4칸이 격자 밖이거나, 독점한 플레이어가 있을 경우
			for(int i = 0; i < 4; i++) {
				int d = cPriority[i];
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	다음 이동할 칸
				
				//	다음 칸이 격자 밖일 경우 skip
				if(!isIn(ny, nx))
					continue;
				
				if(monopolyMap[ny][nx][0] == pNum)	{	//	이동하려는 플레이어가 독점한 칸일 경우
					res = d;
					break;
				}
			}
		}
		
		return res;
	}
	
	//	모든 플레이어가 각자가 있는 위치 독점함
	private static void monopoly() {
		for(int pNum = 1; pNum <= M; pNum++) {
			if(playerList[pNum] == null)	//	이미 죽은 플레이어의 경우
				continue;
			
			monopoly(playerList[pNum]);
		}
	}
	
	//	player가 있는 칸을 독점함
	private static void monopoly(Player player) {
		int y = player.y;
		int x = player.x;
		int pNum = player.num;
		
		monopolyMap[y][x][0] = pNum;	//	pNum번째 플레이어가
		monopolyMap[y][x][1] = K;		//	(y, x) 칸을 K만큼 독점함
	}

	
	private static class Player implements Comparable<Player> {
		public int num;				//	플레이어 번호
		public int y;
		public int x;				//	플레이어 위치
		public int dir;				//	바라보는 방향
		public int[][] priority;	//	바라보는 방향에 따른 이동 우선 순위
		
		public Player(int num, int y, int x) {
			this.num = num;
			this.y = y;
			this.x = x;
			
			this.priority = new int[4][4];
		}
		
		@Override
		public int compareTo(Player other) {	//	번호 큰 플레이어부터 먼저 제거되도록
			return Integer.compare(other.num, this.num);
		}
	}
	
	//	0 : 상, 1 : 하, 2 : 좌, 3 : 우
	private static final int[] dy = {-1, 1, 0, 0};
	private static final int[] dx = {0, 0, -1, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```