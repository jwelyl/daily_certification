# 24_07_25_daily_certification

# Problem Solving (Algorithm & SQL)

### 삼성전자 2020년 상반기 오전 2번 술래잡기 체스 (Fail → Success)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/odd-chess2/description?page=2&pageSize=20)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 4;
	private static final int PMAX = MAX * MAX + 1;
	private static final int EMPTY = 0;		//	도둑말이 없을 경우
	private static final int NONE = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int maxScore = 0;	//	게임 끝날 때 까지 얻을 수 있는 최대 점수
	
	public static void main(String[] args) throws IOException {
		Piece[] thieves = new Piece[PMAX];				//	thieves[pNum] : pNum번째 도둑말, 죽었을 경우 null
		int[][] map = new int[MAX][MAX];				//	map[y][x] : (y, x) 칸에 존재하는 도둑말 번호, 없을 경우 EMPTY 
		Piece police = new Piece(NONE, 0, 0, NONE);		
		
		for(int r = 0; r < MAX; r++) {
			tokens = new StringTokenizer(br.readLine());
			for(int c = 0; c < MAX; c++) {
				int pNum = Integer.parseInt(tokens.nextToken());
				int dir = Integer.parseInt(tokens.nextToken()) - 1;
				
				thieves[pNum] = new Piece(pNum, r, c, dir);
				map[r][c] = pNum;
			}
		}
		
		int initScore = kill(police, thieves, map);	//	(0, 0)에 존재하는 도둑말 죽이고 시작함
		
		simulation(police, thieves, map, initScore);
		
		System.out.println(maxScore);
	}	//	main-end
	
	private static void simulation(Piece police, Piece[] thieves, int[][] map, int score) {
		Piece[] cThieves = copyThieves(thieves);	//	기존 도둑말 복사
		int[][] cMap = copyMap(map);				//	기존 맵 복사
		
		moveAll(police, cThieves, cMap);			//	모든 도둑말들 이동
		List<int[]> posList = checkEnd(police, cThieves, cMap);	//	현재 술래말이 이동할 수 있는 칸
		
		if(posList.isEmpty()) {	//	술래말이 더 이상 이동할 수 없을 경우
			maxScore = Math.max(maxScore, score);	//	최대 점수 갱신
			return;	//	종료
		}
		
		for(int[] pos : posList) {	//	이동할 수 있는 칸에 대해서
			int ny = pos[0];
			int nx = pos[1];		//	술래말이 이동할 칸
			int nscore = score;		//	이동했을 때의 새 점수
			
			Piece nPolice = copyPolice(police);
			Piece[] nThieves = copyThieves(cThieves);
			int[][] nMap = copyMap(cMap);
			
			nPolice.y = ny;
			nPolice.x = nx;
			
			nscore += kill(nPolice, nThieves, nMap);	//	해당 칸으로 이동해서 도둑말 잡고 점수 얻음
			
			simulation(nPolice, nThieves, nMap, nscore);	//	다음 시뮬레이션
		}
	}
	
	//	police가 있는 칸의 도둑말을 제거하고 점수를 얻음
	private static int kill(Piece police, Piece[] thieves, int[][] map) {
		int y = police.y;
		int x = police.x;
		int pNum = map[y][x];
		
		map[y][x] = EMPTY;					//	map에서 도둑말 제거
		police.dir = thieves[pNum].dir;		//	도둑말의 방향을 술래말의 방향으로 함
		thieves[pNum] = null;				//	(y, x)칸에 있던 도둑말 제거
		
		return pNum;	//	잡은 도둑말의 번호만큼 점수를 얻음
	}
	
	//	기존 술래말을 복사해서 새로운 술래말 만듬
	private static Piece copyPolice(Piece origPolice) {
		return new Piece(origPolice);
	}
	
	//	기존 도둑말 리스트 복사해서 새로운 도둑말 리스트 만듬
	private static Piece[] copyThieves(Piece[] origThieves) {
		Piece[] ret = new Piece[PMAX];
		
		for(int pNum = 1; pNum < PMAX; pNum++) {
			if(origThieves[pNum] != null)
				ret[pNum] = new Piece(origThieves[pNum]);
		}
		
		return ret;
	}
	
	//	기존 맵을 복사해서 새로운 맵을 만듬
	private static int[][] copyMap(int[][] origMap) {
		int[][] ret = new int[MAX][MAX];
		for(int r = 0; r < MAX; r++) {
			for(int c = 0; c < MAX; c++)
				ret[r][c] = origMap[r][c];
		}
		
		return ret;
	}
	
	//	모든 도둑말이 이동함
	private static void moveAll(Piece police, Piece[] thieves, int[][] map) {
		for(int pNum = 1; pNum <= MAX * MAX; pNum++) {	//	번호가 작은 도둑말부터 이동함
			Piece thief = thieves[pNum];
			
			if(thief == null)	//	이미 잡힌 도둑말의 경우
				continue;		//	skip
			
			int cy = thief.y;
			int cx = thief.x;			//	현재 도둑말 위치
			int cdir = thief.dir;		//	현재 도둑말 방향
			
			int ndir = NONE;
			int ny = NONE;
			int nx = NONE;
			
			for(int d = 0; d < 8; d++) {
				int moveDir = (cdir + d) % 8;
				int moveY = cy + dy[moveDir];
				int moveX = cx + dx[moveDir];	//	이동하려는 다음 칸
				
				//	다음 칸이 격자 밖이거나, 술래말이 있을 경우
				if(!isIn(moveY, moveX) || moveY == police.y && moveX == police.x)
					continue;
				else {
					ndir = moveDir;
					ny = moveY;
					nx = moveX;
					break;
				}
			}
			
			if(ndir != NONE && ny != NONE && nx != NONE) {	//	현재 도둑말이 이동할 칸이 있을 경우
				if(map[ny][nx] != EMPTY) {	//	가려는 칸에 다른 도둑말이 있을 경우
					int otherNum = map[ny][nx];				//	다른 도둑말 번호
					Piece other = thieves[map[ny][nx]];		//	다른 도둑말
					
					map[cy][cx] = otherNum;	//	현재 칸에 다른 도둑말 옴
					other.y = cy;
					other.x = cx;	//	현재 칸으로 가려는 칸에 있던 다른 말이 옴			
				}
				else	//	가려는 칸에 다른 도둑말이 없을 경우
					map[cy][cx] = EMPTY;	//	현재 칸 비우기
				
				map[ny][nx] = pNum;	//	가려는 칸에 현재 말이 감
				thief.dir = ndir;
				thief.y = ny;
				thief.x = nx;
			}
		}
	}
	
	//	현재 police 위치에서 이동 가능한지 확인
	//	이동 가능할 경우, 이동 가능한 좌표 리스트로 반환
	//	이동 가능한 좌표가 없을 경우, 빈 리스트 반환
	private static List<int[]> checkEnd(Piece police, Piece[] thieves, int[][] map) {
		List<int[]> posList = new ArrayList<>();
		
		int cy = police.y;
		int cx = police.x;
		int cdir = police.dir;
		
		for(int k = 0; ; k++) {
			int ny = cy + dy[cdir] * k;
			int nx = cx + dx[cdir] * k;
			
			if(!isIn(ny, nx))	//	다음 칸이 격자 밖일 경우, 이 방향으로는 더 이상 확인 불가
				break;
			
			if(map[ny][nx] == EMPTY)	//	다음 칸이 빈 칸일 경우, 그 다음 칸으로 넘어가기
				continue;
			
			posList.add(new int[] {ny, nx});	//	도둑말 있는 칸 넣기
		}
		
		return posList;
	}

	private static final int[] dy = {-1, -1, 0, 1, 1, 1, 0, -1};
	private static final int[] dx = {0, -1, -1, -1, 0, 1, 1, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < MAX) && (0 <= x && x < MAX);
	}
	
	private static class Piece {
		public int pNum;	//	말 번호
		public int y;
		public int x;		//	말 좌표
		public int dir;		//	말이 바라보는 방향
		
		public Piece(int pNum, int y, int x, int dir) {
			this.pNum = pNum;
			this.y = y;
			this.x = x;
			this.dir = dir;
		}
		
		public Piece(Piece piece) {
			this(piece.pNum, piece.y, piece.x, piece.dir);
		}
	}
}	//	Main-class-end
```

### 삼성전자 2019년 하반기 오후 2번 윷놀이 사기단 (Fail → Success)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/woodstick-fraud/description?page=2&pageSize=20)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int END = -1;	//	도착했을 경우
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	//	board[i] : i번째 칸에 멈췄을 때 얻을 수 있는 점수
	//	0 : 경로 1 시작 칸
	//	5 : 경로 2로 방향 전환 칸 -> 22
	//	10 : 경로 3으로 방향 전환 칸 -> 31
	//	15 : 경로 4로 방향 전환 칸 -> 39
	//	21 : 경로 1 종료 칸
	//	30 : 경로 2 종료 칸
	//	38 : 경로 3 종료 칸
	//	47 : 경로 4 종료 칸
	private static final int[] board = {
		//	경로 1. 0 ~ 21
		0, 2, 4, 6, 8, 0, 12, 14, 16, 18, 0, 22, 24, 26, 28, 0, 32, 34, 36, 38, 40, 0,
		//	경로 2. 22 ~ 30
		10, 13, 16, 19, 25, 30, 35, 40, 0,
		//	경로 3. 31 ~ 38
		20, 22, 24, 25, 30, 35, 40, 0,
		//	경로 4. 39 ~ 47
		30, 28, 27, 26, 25, 30, 35, 40, 0 
	};	
	
	private static final int[] pos = new int[4];
	private static final boolean[] visited = new boolean[48];
	
	private static int maxScore = 0;	//	최대로 얻을 수 있는 점수
	
	private static final int[] yut = new int[10];
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		for(int i = 0; i < 10; i++)
			yut[i] = Integer.parseInt(tokens.nextToken());
		
		simulation(0, 0);
		
		System.out.println(maxScore);
	}	//	main-end
	
	private static void simulation(int nth, int score) {
		if(nth == 10) {	//	10번 모두 던졌을 경우
			maxScore = Math.max(maxScore, score);
			return;
		}
		
		for(int pNum = 0; pNum < 4; pNum++) {	//	pNum번째 말을 이동한다고 가정
			int curPos = pos[pNum];	//	pNum번째 말의 현재 위치
			
			if(curPos == END)	//	pNum번째 말이 이미 도착점에 도달한 말일 경우, 이동 불가
				continue;
				
			int nextPos = nextPos(curPos, yut[nth]);	//	현재 말이 이동할 다음 칸
			int nscore = score;			//	이동 후 점수
			
			if(nextPos != END) {	//	다음 칸이 도착점이 아닐 경우 
				if(visited[nextPos])	//	다음 칸에 이미 다른 말이 있을 경우, pNum번째 말을 던질 수 없음
					continue;
				
				nscore += board[nextPos];	//	pNum번째 말이 이동하여 점수 추가
				pos[pNum] = nextPos;		//	말들 위치 갱신
				setVisited(curPos, false);	//	현재 칸에서 이동
				setVisited(nextPos, true);	//	다음 칸 방문 처리
				
				simulation(nth + 1, nscore);	//	다음 윳 던지기
				
				setVisited(nextPos,false);	//	방문처리 취소
				setVisited(curPos, true);	//	원상복귀
				pos[pNum] = curPos;
			}
			else {	//	다음 칸이 도착점일 경우
				pos[pNum] = END;	//	다음 던지기에서는 pNum번째 말 못 움직이게 처리
				
				setVisited(curPos, false);	//	현재 칸에서 이동
				simulation(nth + 1, nscore);
				setVisited(curPos, true);	//	원상복귀
				
				pos[pNum] = curPos;
			}
		}
	}
	
	private static void setVisited(int pos, boolean status) {
		//	경로 2, 3, 4 모두 지나는 칸 방문 상태 변경
		if(pos == 26 || pos == 34 || pos == 43) {
			visited[26] = status;
			visited[34] = status;
			visited[43] = status;
		}
		else if(pos == 27 || pos == 35 || pos == 44) {
			visited[27] = status;
			visited[35] = status;
			visited[44] = status;
		}
		else if(pos == 28 || pos == 36 || pos == 45) {
			visited[28] = status;
			visited[36] = status;
			visited[45] = status;
		}
		//	경로 1, 2, 3, 4 모두 지나는 칸 방문 상태 변경
		else if(pos == 20 || pos == 29 || pos == 37 || pos == 46) {
			visited[20] = status;
			visited[29] = status;
			visited[37] = status;
			visited[46] = status;
		}
		else visited[pos] = status;	//	그 외 칸 방문 상태 변경 
	}
	
	//	현재 위치가 curPos이고 eyes의 칸이 나왔을 때 다음 위치, 도착점에 도달한 경우 END  반환
	private static int nextPos(int curPos, int eyes) {
		if(0 <= curPos && curPos < 21) {	//	경로 1에 위치할 경우
			if(curPos + eyes == 5)	//	경로 2로 꺾어야 할 경우
				return 22;
			else if(curPos + eyes == 10)	//	경로 3으로 꺾어야 할 경우
				return 31;
			else if(curPos + eyes == 15)	//	경로 4로 꺾어야 할 경우
				return 39;
			else if(curPos + eyes >= 21)		//	도착점에 도달한 경우
				return END;
		}
		else if(22 <= curPos && curPos < 30) {	//	경로 2에 위치할 경우
			if(curPos + eyes >= 30)	//	도착점에 도달한 경우
				return END;
		}
		else if(31 <= curPos && curPos < 38) {	//	경로 3에 위치할 경우
			if(curPos + eyes >= 38)	//	도착점에 도달한 경우
				return END;
		}
		else if(39 <= curPos && curPos < 47) {	//	경로 4에 위치할 경우
			if(curPos + eyes >= 47)	//	도착점에 도달한 경우
				return END;
		}
		
		return curPos + eyes;
	}
}	//	Main-class-end
```