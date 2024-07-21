# 24_07_21_daily_certification

# Problem Solving (Algorithm & SQL)

### 삼성전자 2022년 하반기 오전 1번 싸움땅

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/battle-ground/description?page=1&pageSize=20)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Collections;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int EMPTY = 0;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static int N;										//	땅 격자 크기
	private static int M;										//	플레이어 수
	private static int K;										//	라운드 수
	private static PriorityQueue<Integer>[][] gunMap;			//	gunMap[i][j] : (i, j)에 놓인 총들의 공격력, 공격력 높은 총이 우선
	private static int[][] playerMap;							//	playerMap[i][j] : (i, j)에 위치한 플레이어 번호
	private static Player[] players;							//	players[i] : i번째 플레이어
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		N = Integer.parseInt(tokens.nextToken());
		M = Integer.parseInt(tokens.nextToken());
		K = Integer.parseInt(tokens.nextToken());

		gunMap = new PriorityQueue[N][N];
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++)
				gunMap[r][c] = new PriorityQueue<Integer>(Collections.reverseOrder());
		}
		
		playerMap = new int[N][N];
		players = new Player[M + 1];
		
		for(int r = 0; r < N; r++) {
			tokens = new StringTokenizer(br.readLine());
			for(int c = 0; c < N; c++) {
				int gunPower = Integer.parseInt(tokens.nextToken());
				
				if(gunPower > EMPTY)	//	해당 칸에 총이 존재할 경우
					gunMap[r][c].offer(gunPower);
			}
		}

		for(int pNum = 1; pNum <= M; pNum++) {
			tokens = new StringTokenizer(br.readLine());
			int x = Integer.parseInt(tokens.nextToken()) - 1;
			int y = Integer.parseInt(tokens.nextToken()) - 1;
			int d = Integer.parseInt(tokens.nextToken());
			int s = Integer.parseInt(tokens.nextToken());
			
			players[pNum] = new Player(pNum, x, y, d, s);
			playerMap[x][y] = pNum;	//	pNum번 플레이어를 (x, y) 칸에 배치
		}
		
		for(int k = 0; k < K; k++)
			simulation(k + 1);
		
		for(int pNum = 1; pNum <= M; pNum++)
			sb.append(players[pNum].point).append(" ");

		System.out.print(sb);
	}	//	main-end
	
	//	한 라운드를 진행함
	private static void simulation(int round) {
		for(int pNum = 1; pNum <= M; pNum++) {
			Player cPlayer = players[pNum];
			
			playerMap[cPlayer.x][cPlayer.y] = EMPTY;	//	일단 현재 칸 비우기
			
			//	1. 현재 플레이어가 한 칸 이동
			move(cPlayer);

			
			int cx = cPlayer.x;
			int cy = cPlayer.y;	//	현재 플레이어가 이동한 칸
			
			//	2-1. 현재 플레이어가 이동한 칸에 다른 플레이어가 없을 경우
			if(playerMap[cx][cy] == EMPTY) {
				//	2-1-1. 현재 플레이어가 이동한 칸에 총이 있을 경우
				if(!gunMap[cx][cy].isEmpty()) {
					int maxGun = gunMap[cx][cy].peek();	//	현재 칸에 존재하는 총 중 가장 공격력이 높은 총
					
					if(cPlayer.gun == 0)	//	현재 플레이어가 총이 없을 경우
						cPlayer.gun = gunMap[cx][cy].poll();	//	현재 칸의 가장 강한 총을 가져감
					else if(maxGun > cPlayer.gun) {	//	현재 플레이어가 총이 있지만 현재 칸의 가장 강한 총보다는 약할 경우
						gunMap[cx][cy].offer(cPlayer.gun);	//	현재 플레이어가 가진 총을 내려놓음
						cPlayer.gun = gunMap[cx][cy].poll();
					}
				}
				
				playerMap[cx][cy] = pNum;	//	(cx, cy)칸에는 pNum 플레이어가 위치함
			}
			else {	//	2-2. 다른 플레이어가 있을 경우
				int otherNum = playerMap[cx][cy];			//	다른 플레이어 번호
				Player otherPlayer = players[otherNum];		//	다른 플레이어
				
				if(cPlayer.fight(otherPlayer))	//	현재 플레이어가 이긴 경우
					result(cPlayer, otherPlayer, pNum, otherNum);
				else	//	다른 플레이어가 이긴 경우
					result(otherPlayer, cPlayer, otherNum, pNum);
			}
		}
	}
	
	//	승자는 포인트를 얻음
	//	패자는 총을 내려놓고 이동함
	//	승자는 해당 칸에 있는 총 중 가장 강한 총으로 갈아탐
	private static void result(Player winner, Player loser, int winnerNum, int loserNum) {
		int add = (winner.s + winner.gun) - (loser.s + loser.gun);	//	승자가 얻게 될 포인트
		winner.point += add;
		
		if(loser.gun > 0) {	//	패자가 총을 가지고 있을 경우
			gunMap[loser.x][loser.y].offer(loser.gun);	//	패자가 가지고 있던 총을 현재 칸에 내려놓음
			loser.gun = 0;
		}
		
		//	패자는 다른 칸으로 이동함
		while(true) {
			int nx = loser.x + dx[loser.d];
			int ny = loser.y + dy[loser.d];	//	이동할 다른 칸
			
			if(!isIn(nx, ny) || playerMap[nx][ny] != EMPTY) {	//	이동할 다른 칸이 범위 밖이거나, 다른 플레이어가 있을 경우
				loser.d = turn90(loser.d);	//	이동 방향 오른쪽으로 90도 회전
				continue;
			}
			
			if(!gunMap[nx][ny].isEmpty())	//	이동할 칸에 총이 있다면
				loser.gun = gunMap[nx][ny].poll();	//	가장 강한 총 획득하기
		
			loser.x = nx;
			loser.y = ny;
			playerMap[loser.x][loser.y] = loserNum;	//	패배한 플레이어는 이동
			break;
		}
		
		//	승자는 현재 칸에 놓인 총기 중 가장 강한 총기로 갈아탐
		if(!gunMap[winner.x][winner.y].isEmpty() && winner.gun < gunMap[winner.x][winner.y].peek()) {	//	현재 가지고 있는 총보다 바닥에 놓인 총이 더 강할 경우
			gunMap[winner.x][winner.y].offer(winner.gun);
			winner.gun = gunMap[winner.x][winner.y].poll();
		}
		
		playerMap[winner.x][winner.y] = winnerNum;
	}
	
	//	player가 이동
	private static void move(Player player) {
		int nx = player.x + dx[player.d];
		int ny = player.y + dy[player.d];	//	플레이어가 바라 보는 방향으로 한 칸 이동할 칸
		
		if(!isIn(nx, ny)) {	//	이동할 칸이 격자 밖인 경우
			player.d = reverseDir(player.d);	//	플레이어 반대 방향 보도록 변경
			nx = player.x + dx[player.d];
			ny = player.y + dy[player.d];		//	반대 방향으로 한 칸 이동
		}
		
		player.x = nx;
		player.y = ny;
	}
	
	private static int[] dx = {-1, 0, 1, 0};
	private static int[] dy = {0, 1, 0, -1};
	
	private static boolean isIn(int x, int y) {
		return (0 <= x && x < N) && (0 <= y && y < N);
	}
	
	//	dir의 반대 방향 찾기
	private static int reverseDir(int dir) {
		return (dir < 2) ? dir + 2 : dir - 2; 
	}
	
	//	dir에서 오른쪽으로 90도 회전한 방향 찾기
	private static int turn90(int dir) {
		return (dir + 1) % 4;
	}
	
	private static class Player {
		public final int num;		//	플레이어 번호
		public int x;
		public int y;		//	플레이어 위치
		public int d;		//	플레이어가 바라보는 방향
		public final int s;	//	플레이어 초기 능력치
		public int gun;		//	플레이어가 가지고 있는 총기 공격력
		public int point;	//	플레이어가 획득한 포인트
		
		public Player(int num, int x, int y, int d, int s) {
			this.num = num;
			this.x = x;
			this.y = y;
			this.d = d;
			this.s = s;
		}
		
		//	this 플레이어랑 other 플레이어가 싸워서 this가 이기면 true, other가 이기면 false
		public boolean fight(Player other) {
			if(this.s + this.gun > other.s + other.gun)	//	(초기 능력치 + 총기 공격력)이 더 클 경우
				return true;
			else if(this.s + this.gun < other.s + other.gun)	//	(초기 능력치 + 총기 공격력)이 더 작을 경우
				return false;
			else {	//	(초기 능력치 + 총기 공격력)이 같을 경우
				if(this.s > other.s)	//	this의 초기 능력치가 높을 경우
					return true;
				else					//	other의 초기 능력치가 높을 경우
					return false;
			}
		}
	}
}	//	Main-class-end
```

### 삼성전자 2022년 하반기 오전 2번 산타의 선물 공장

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/battle-ground/description?page=1&pageSize=20)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static int Q;			//	명령어 개수
	private static int N;			//	상자 개수
	private static int M;			//	컨베이어 벨트 개수
	
	private static Box[] heads;	//	컨베이어 벨트의 head
	private static Box[] tails;	//	컨베이어 벨트의 tail
	private static boolean[] brokens;	//	broken[i] : i번째 컨베이어 벨트가 고장났을 경우 true
	private static final Map<Integer, Box> boxMap = new HashMap<>();	//	Key : 상자 ID, Value : 해당 상자 객체
	private static final Map<Integer, Integer> beltMap = new HashMap<>();	//	Key : 상자 ID, Value : 해당 상자가 있는 벨트 번호
	
	public static void main(String[] args) throws IOException {
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < Q; q++) {
			tokens = new StringTokenizer(br.readLine());
			int cmd = Integer.parseInt(tokens.nextToken());
			
			switch(cmd) {
			case 100:	//	초기화
				init();
				break;
			case 200:	//	물건 하차
				int wMax = Integer.parseInt(tokens.nextToken());
				
				sb.append(unload(wMax)).append("\n");
				break;
			case 300:	//	물건 제거
				int rId = Integer.parseInt(tokens.nextToken());
				
				sb.append(removeBox(rId)).append("\n");
				break;
			case 400:	//	물건 확인
				int fId = Integer.parseInt(tokens.nextToken());
				
				sb.append(findBox(fId)).append("\n");
				break;
			case 500:	//	벨트 고장
				int bNum = Integer.parseInt(tokens.nextToken());
				
				sb.append(breakBelt(bNum)).append("\n");
				break;
			}
		}
		
		System.out.print(sb);
	}	//	main-end
	
	//	100. 공장 설립
	private static void init() {
		N = Integer.parseInt(tokens.nextToken());	//	N개의 상자
		M = Integer.parseInt(tokens.nextToken());	//	M개의 컨베이어 벨트
		
		heads = new Box[M + 1];
		tails = new Box[M + 1];
		brokens = new boolean[M + 1];
		
		for(int bNum = 1; bNum <= M; bNum++) {
			heads[bNum] = new Box(NONE, NONE);
			tails[bNum] = new Box(NONE, NONE);	//	각 벨트의 head, tail dummy node 추가
			
			heads[bNum].next = tails[bNum];
			tails[bNum].prev = heads[bNum];	//	head와 tail을 연결하여 빈 벨트 생성
		}
		
		int[] ids = new int[N + 1];
		int[] ws = new int[N + 1];
		
		for(int i = 1; i <= N; i++)
			ids[i] = Integer.parseInt(tokens.nextToken());
		for(int i = 1; i <= N; i++)
			ws[i] = Integer.parseInt(tokens.nextToken());
		
		for(int i = 0; i < M; i++) {	//	i + 1 번째 벨트
			int start = i * (N / M) + 1;
			int end = (i + 1) * (N / M);
			
			for(int j = start; j <= end; j++) {
				Box box = new Box(ids[j], ws[j]);	//	i + 1번째 벨트의 가장 마지막에 삽입
				boxMap.put(box.id, box);
				pushBack(i + 1, box);
			}
		}
	}
	
	//	200. 물건 하차
	//	각 벨트의 가장 앞에 있는 물건 중 무게가 wMax 이하인 것들을 하차시킴
	//	wMax 초과의 물건은 해당 벨트의 가장 뒤로 보냄
	//	하차한 물건들의 무게 합을 반환
	private static long unload(int wMax) {
		long sum = 0L;
		
		for(int bNum = 1; bNum <= M; bNum++) {
			if(isEmpty(bNum) || isBroken(bNum))	//	빈 벨트이거나, 고장난 벨트는 skip
				continue;
			
			Box first = popFront(bNum);	//	bNum의 가장 앞에 있는 상자
			
			if(first.w <= wMax)	//	wMax 이하 상자일 경우
				sum += first.w;	//	하차 시킴
			else	//	wMax 초과 상자일 경우
				pushBack(bNum, first);	//	해당 벨트 가장 마지막에 다시 삽입함
		}
		
		return sum;
	}
	
	//	300. 물건 제거
	//	rId에 해당하는 상자 객체를 벨트에서 제거한다.
	public static int removeBox(int rId) {
		if(!boxMap.containsKey(rId))	//	그런 상자가 없을 경우
			return NONE;
		
		if(beltMap.getOrDefault(rId, NONE) == NONE)	//	rId 상자가 이미 어떤 벨트에도 존재하지 않는 경우
			return NONE;
		
		Box rBox = boxMap.get(rId);	//	제거할 상자
		Box before = rBox.prev;		//	제거할 상자 이전 상자
		Box after = rBox.next;		//	제거할 상자 다음 상자
		
		before.next = after;
		after.prev = before;	//	before와 after 연결
		
		rBox.prev = null;
		rBox.next = null;			//	rBox 연결 제거
		beltMap.put(rId, NONE);		//	rBox는 어떤 벨트에도 없음
		
		return rId;
	}
	
	//	400. 물건 확인
	private static int findBox(int fId) {
		if(!boxMap.containsKey(fId))	//	그런 상자가 없을 경우
			return NONE;
		
		int bNum = beltMap.getOrDefault(fId, NONE);	//	fId 상자가 존재하는 벨트 번호
		
		if(bNum == NONE)	//	fId 상자가 이미 어떤 벨트에도 존재하지 않는 경우
			return NONE;
		
		Box first = boxMap.get(fId);	//	이동시킬 가장 앞의 상자 (가장 앞의 상자가 되어야 함)
		Box last = tails[bNum].prev;	//	이동시킬 가장 마지막 상자
		
		if(first == heads[bNum].next)	//	first가 해당 벨트의 첫 번째 상자일 경우 옮길 필요 없음
			return bNum;
		
		Box pos1 = heads[bNum].next;	//	기존 가장 앞의 상자 (last 뒤의 상자가 되어야 함)
		Box pos2 = first.prev;			//	기존 first 앞의 상자 (가장 마지막 상자가 되어야 함)
		
		pos2.next = tails[bNum];
		tails[bNum].prev = pos2;	//	pos2와 tail 연결
		
		heads[bNum].next = first;
		first.prev = heads[bNum];	//	head와 first 연결
		last.next = pos1;
		pos1.prev = last;			//	last와 pos1 연결
		
		return bNum;
	}
	
	//	500. 벨트 고장
	private static int breakBelt(int bNum) {
		if(isBroken(bNum))	//	이미 고장난 벨트인 경우
			return NONE;
		
		brokens[bNum] = true;	//	고장 처리
		
		if(isEmpty(bNum))	//	고장난 벨트가 빈 벨트일 경우
			return bNum;	//	별도 처리 필요 없음
		
		int toBelt = NONE;	//	현재 bNum 벨트에 있는 상자들을 옮길 정상 벨트 번호
		for(int num = bNum + 1; num <= M; num++) {	//	오른쪽 벨트 중 정상 벨트 찾기
			if(isBroken(num))
				continue;
			
			toBelt = num;
			break;
		}
		
		if(toBelt == NONE) {	//	오른쪽 벨트는 전부 고장난 경우
			for(int num = 1; num < bNum; num++) {	//	왼쪽 벨트 중 정상 벨트 찾기
				if(isBroken(num))
					continue;
				
				toBelt = num;
				break;
			}
		}
		
		Box fromFirst = heads[bNum].next;	//	고장난 벨트의 첫 번째 상자
		Box fromLast = tails[bNum].prev;	//	고장난 벨트의 마지막 상자
		
		Box cur = fromFirst;
		while(cur != tails[bNum]) {
			beltMap.put(cur.id, toBelt);	//	고장난 벨트 위의 상자가 존재하는 벨트 번호 갱신
			cur = cur.next;
		}
		
		Box toLast = tails[toBelt].prev;	//	옮겨갈 벨트의 마지막 상자
		
		toLast.next = fromFirst;
		fromFirst.prev = toLast;	//	기존 벨트의 첫 번째 상자를 옮겨갈 벨트의 마지막 상자 뒤에 연결
		fromLast.next = tails[toBelt];
		tails[toBelt].prev = fromLast;	//	기존 벨트의 마지막 상자를 옮겨갈 벨트의 tail과 연결
		
		heads[bNum].next = tails[bNum];
		tails[bNum].prev = heads[bNum];	//	고장난 벨트 빈 벨트로 만들기
		
		return bNum;
	}

	//	bNum 벨트가 비어있는지 확인
	private static boolean isEmpty(int bNum) {
		return heads[bNum].next == tails[bNum] && tails[bNum].prev == heads[bNum];
	}
	
	//	bNum 벨트가 고장났는지 확인
	private static boolean isBroken(int bNum) {
		return brokens[bNum];
	}
	
	//	bNum 벨트의 가장 처음 상자를 제거하고 반환함
	//	bNum 벨트가 빈 벨트거나 고장나지 않은 벨트일때만 호출됨
	private static Box popFront(int bNum) {
		Box first = heads[bNum].next;	//	제거할 처음 상자
		Box nFirst = first.next;		//	bNum 벨트에서 처음 상자가 될 상자 (기존의 두 번째 상자)
		
		heads[bNum].next = nFirst;
		nFirst.prev = heads[bNum];	//	head와 nFirst 연결
		
		first.prev = null;
		first.next = null;	//	first의 연결 끊기
		
		beltMap.put(first.id, NONE);	//	first 상자를 bNum 벨트에서 제거
		
		return first;
	}
	
	//	bNum 벨트의 가장 마지막에 box를 삽입
	private static void pushBack(int bNum, Box box) {
		Box before = tails[bNum].prev;	//	기존 bNum 벨트의 가장 마지막 상자
		
		before.next = box;
		box.prev = before;	//	before과 box 연결
	
		tails[bNum].prev = box;
		box.next = tails[bNum];	//	box와 tail 연결
		
		beltMap.put(box.id, bNum);	//	box는 bNum 벨트에 존재함
	}
	
	private static class Box {
		public final int id;	//	상자 고유 번호
		public final int w;		//	상자 무게
		
		public Box prev;		//	이전 상자
		public Box next;		//	다음 상자
		
		public Box(int id, int w) {
			this.id = id;
			this.w = w;
			this.prev = null;
			this.next = null;
		}
	}
}	//	Main-class-end
```