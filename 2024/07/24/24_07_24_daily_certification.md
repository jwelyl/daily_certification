# 24_07_24_daily_certification

# Problem Solving (Algorithm & SQL)

### 삼성전자 2020년 상반기 오후 1번 승자독식 모노폴리 (Fail → Success)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/odd-monopoly/description?page=2&pageSize=20)

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
	
	private static Player[] playerList;					//	playerList[num] : num번째 플레이어
	
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
		remain = M;
		
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
					Player player = new Player(pNum, r, c);	//	num번째 플레이어 초기 배치
					playerList[pNum] = player;
					playerMap[r][c].offer(player);			//	(r, c)에 num번째 플레이어 배치
					monopoly(player);						//	num번째 플레이가 배치된 위치 독점함
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