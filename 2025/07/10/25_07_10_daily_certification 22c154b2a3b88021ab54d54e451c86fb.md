# 25_07_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17081 RPG Extreme

[17081번: RPG Extreme](http://boj.ma/17081/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final int WIN = 1;
	private static final int CONT = 0;
	private static final int LOSE = -1;
	
	private static final char EMPTY = '.';
	private static final char WALL = '#';
	private static final char ITEM = 'B';
	private static final char SPIKE = '^';
	private static final char MONSTER = '&';
	private static final char BOSS = 'M';
	private static final char PLAYER = '@';
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K;	//	몬스터 수
	private static int L;	//	아이템 상자 수

	private static char[][] map;
	
	private static int res = CONT;
	
	//	monsterMap[y][x] : (y, x) 칸에 존재하는 몬스터
	private static Monster[][] monsterMap;
	//	itemMap[y][x] : (y, x) 칸에 존재하는 아이템
	private static Item[][] itemMap;
	
	//	이동 명령어
	private static char[] opt;
	
	private static final Player player = new Player();
	private static int turn = 1;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
	
		map = new char[N][M];
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for(int x = 0; x < M; x++) {
				if(map[y][x] == PLAYER) {
					player.sy = y;
					player.sx = x;	//	주인공 최초 위치
					player.y = y;
					player.x = x;	//	주인공 현재 위치
					map[player.sy][player.sx] = EMPTY;
				}
				else if(map[y][x] == MONSTER || map[y][x] == BOSS)
					K++;
				else if(map[y][x] == ITEM)
					L++;
			}
		}
		opt = br.readLine().toCharArray();
		
		monsterMap = new Monster[N][M];
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			int r = Integer.parseInt(st.nextToken()) - 1;
			int c = Integer.parseInt(st.nextToken()) - 1;
			String name = st.nextToken();
			int atk = Integer.parseInt(st.nextToken());
			int def = Integer.parseInt(st.nextToken());
			int maxHp = Integer.parseInt(st.nextToken());
			int exp = Integer.parseInt(st.nextToken());
			
			monsterMap[r][c] = new Monster(name, atk, def, maxHp, exp, map[r][c] == BOSS);
		}
		
		itemMap = new Item[N][M];
		for(int l = 0; l < L; l++) {
			st = new StringTokenizer(br.readLine());
			int r = Integer.parseInt(st.nextToken()) - 1;
			int c = Integer.parseInt(st.nextToken()) - 1;
			String t = st.nextToken();	//	아이템 종류
			String s = st.nextToken();	//  공격력/방어력 수치 or 장신구 종류
			
			itemMap[r][c] = new Item(t, s);
		}

		for(char dir : opt) {
			res = player.move(dir);
			if(res == WIN || res == LOSE)
				break;
			
			turn++;
		}
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++) {
				if(y == player.y && x == player.x) {
					if(res != LOSE)
						sb.append(PLAYER);
					else
						sb.append(map[y][x]);
				}
				else
					sb.append(map[y][x]);
			}
			sb.append("\n");
		}
		sb.append("Passed Turns : ").append(res == CONT ? turn - 1 : turn).append("\n");
		
		sb.append(player);
		
		if(res == LOSE)
			sb.append("YOU HAVE BEEN KILLED BY ").append(player.death).append("..");
		else
			sb.append(res == WIN ? "YOU WIN!" : "Press any key to continue.");
		
		System.out.println(sb);
	} //	main-end
	
	private static class Player {
		public int maxHp;	//	주인공의 최대 체력
		public int hp;		//	주인공의 현재 체력
		
		public int level;	//	주인공 레벨
		public int exp;	//	주인공 보유 경험치
		public int reqExp;	//	레벨업을 위해 필요한 경험치
		
		public int atk;	//	주인공 자체 공격력
		public int wAtk;	//	주인공이 착용한 무기의 공격력
		
		public int def;	//	주인공 방어력
		public int aDef;	//	주인공이 착용한 방어구 방어력
		
		public int sy;
		public int sx;	//	주인공 최초 위치
		public int y;
		public int x;	//	주인공 위치
		
		//	각 장신구 목록, 활성화되면 true
		public final Set<String> accSet;
		
		public String death;	//	죽음의 이유
		
		public Player() {
			this.maxHp = 20;
			this.hp = maxHp;
			this.atk = 2;
			this.def = 2;
			this.level = 1;
			this.reqExp = this.level * 5;
			this.accSet = new HashSet<>();
		}
		
		//	dir 방향으로 이동하고 그 결과를 보고함
		//	1 : 이동 결과 게임에서 승리함
		//	0 : 이동 후에도 게임이 끝나지 않고 계속됨
		//	-1 : 이동 결과 게임에서 패배함
		public int move(char dir) {
			int[] nextPos = nextPos((dir == 'R' ? 0 : (dir == 'D' ? 1 : (dir == 'L' ? 2 : 3))));
			int ny = nextPos[0];
			int nx = nextPos[1];
			
			if(!isIn(ny, nx) || map[ny][nx] == WALL) {	//	이동하려는 칸이 범위 밖이거나 벽일 경우, 원래 칸으로 돌아가기 
				if(map[this.y][this.x] == SPIKE)	//	돌아가는 원래 칸이 가시일 경우
					return spike();		//	가시 밞은 결과 반환
				
				return CONT;	//	원래 칸으로 돌아가면서 아무 일도 안생김
			}
			else if(map[ny][nx] == EMPTY) {	//	이동하려는 칸이 빈 칸일 경우
				this.y = ny;
				this.x = nx;	//	그냥 이동하기
				
				return CONT;	//	빈칸으로 가면서 아무 일도 안생김
			}
			else if(map[ny][nx] == SPIKE) {	//	이동하려는 칸이 가시일 경우
				this.y = ny;
				this.x = nx;
				
				return spike();
			}
			else if(map[ny][nx] == ITEM) {	//	이동하려는 칸에 아이템이 있을 경우
				this.y = ny;
				this.x = nx;
				return getItem(ny, nx);
			}
			else	//	이동하려는 칸에 몬스터가 있을 경우
				return fight(ny, nx);
		}
		
		public int fight(int ny, int nx) {
			Monster monster = monsterMap[ny][nx];
			boolean isBoss = monster.isBoss;
			int res = CONT;
			
			if(accSet.contains("HU") && isBoss)	//	보스전이고 HU 장신구가 있을 경우
				this.hp = this.maxHp;
			
			//	홀수 턴에는 주인공 공격, 짝수 턴에는 몬스터 공격
			for(int t = 1; ; t++) {
				int pAtk = this.atk + this.wAtk;	//	주인공이 몬스터에게 입히는 데미지
				int pDef = this.def + this.aDef;	//	주인공의 실제 방어력
				int mAtk = monster.atk;	//	몬스터가 주인공에게 입히는 데미지
				int mDef = monster.def; //	몬스터의 방어력
				
				if(t == 1 && accSet.contains("CO")) {	//	첫번째 공격이고 CO 아이템이 있을 경우
					if(accSet.contains("DX"))	//	DX 아이템도 있을 경우
						pAtk *= 3;
					else
						pAtk *= 2;
				}
				
				if(t % 2 == 1) {	//	주인공이 공격할 차례
					int pRealAtk = Math.max(1, pAtk - mDef);	//	주인공이 몬스터에게 실제로 입히는 데미지
					
					if(pRealAtk >= monster.hp) {	//	이번 공격에 몬스터를 잡는 경우
						this.y = ny;
						this.x = nx;	//	몬스터가 있는 칸으로 이동
						monsterMap[ny][nx] = null;
						map[ny][nx] = EMPTY;		//	몬스터는 사라짐
						
						if(accSet.contains("HR"))	//	HR 아이템이 있을 경우
							this.hp = Math.min(this.hp + 3, this.maxHp);	//	체력 3 회복
						if(accSet.contains("EX"))	//	EX 아이템이 있을 경우
							this.exp += (int)(1.2 * monster.exp);	//	얻는 경첨치 1.2배
						else
							this.exp += monster.exp;
						
						if(canLevelUp()) //	레벨업할 수 있을 경우
							levelUp();
						
						//	보스 몬스터를 잡는 경우 게임 승리
						res = isBoss ? WIN : CONT;
						break;
					}
					else
						monster.hp -= pRealAtk;
				}
				else {	//	몬스터가 공격할 차례
					int mRealAtk = Math.max(1, mAtk - pDef);	//	몬스터가 주인공에게 실제로 입히는 데미지
					if(isBoss && t == 2 && accSet.contains("HU")) 	//	보스 몬스터이고, 보스 몬스터의 첫번째 공격이고, HU 장신구가 있을 경우
						mRealAtk = 0;	//	보스 몬스터의 첫번째 공격 무효화
					
					this.hp -= mRealAtk;	//	몬스터에게 피격 당함
					
					if(isDead()) {	//	피격당해서 죽게 될 경우
						if(canRevive()) {	//	부활할 수 있을 경우
							monster.hp = monster.maxHp;	//	몬스터는 최대 체력 회복함
							res = revive();
							break;
						}
						
						if(isBoss)
							this.death = "Boss";
						else
							this.death = monster.name;
						
						res = LOSE;
						
						break;
					}
				}
			}
			
			return res;
		}
		
		public int getItem(int ny, int nx) {
			Item item = itemMap[ny][nx];
			
			if(item.item.equals("O")) {	//	장신구인 경우
				if(accSet.size() < 4) {	//	착용한 장신구가 4개 미만일 경우
					String sort = item.sort;
					accSet.add(sort);
				}
			}
			else if(item.item.equals("W"))	//	무기일 경우
				this.wAtk = item.ability;	//	무기 교체
			else							//	방어구일 경우
				this.aDef = item.ability;	//	방어구 교체
			
			//	장신구가 4개여서 얻지 못해도 장신구는 사라짐
			itemMap[ny][nx] = null;	//	얻은 아이템은 itemMap에서 사라짐
			map[ny][nx] = EMPTY;	//	얻은 아이템은 지도에서 사라짐
			
			return CONT;
		}
		
		public int spike() {	//	가시를 밟을 경우
			this.hp -= accSet.contains("DX") ? 1 : 5;
			
			if(this.isDead()) {
				if(canRevive())
					return revive();
				
				this.death = "SPIKE TRAP";
				return LOSE;
			}
			
			return CONT;
		}
		
		//	죽게 되는지 확인
		public boolean isDead() {
			return this.hp <= 0;
		}
		
		//	부활할 수 있는지 확인
		public boolean canRevive() {
			return accSet.contains("RE");
		}
		
		public int revive() {
			
			this.hp = this.maxHp;	//	체력을 최대로 회복함
			this.y = this.sy;
			this.x = this.sx;	//	처음 위치로 돌아감
			
			accSet.remove("RE");
		
			return  CONT;
		}
		
		//	레벨업 할 수 있는지 확인
		public boolean canLevelUp() {
			return this.exp >= this.reqExp;
		}
		
		//	레벨업할 수 있을 경우 레벨업
		public void levelUp() {
			this.level++;
			this.maxHp += 5;
			this.hp = this.maxHp;
			this.atk += 2;
			this.def += 2;
			this.reqExp = this.level * 5;
			this.exp = 0;
		}
		

		private int[] nextPos(int dir) {
			int ny = y + dy[dir];
			int nx = x + dx[dir];
			
			return new int[] {ny, nx};
		}
		
		private static final int[] dy = {0, 1, 0, -1};
		private static final int[] dx = {1, 0, -1, 0};
		
		private static boolean isIn(int y, int x) {
			return (0 <= y && y < N) && (0 <= x && x < M);
		}
		
		@Override
		public String toString() {
			StringBuilder sb = new StringBuilder();
			
			sb.append("LV : ").append(this.level).append("\n");
			sb.append("HP : ").append(Math.max(this.hp, 0)).append("/").append(this.maxHp).append("\n");
			sb.append("ATT : ").append(this.atk).append("+").append(this.wAtk).append("\n");
			sb.append("DEF : ").append(this.def).append("+").append(this.aDef).append("\n");
			sb.append("EXP : ").append(this.exp).append("/").append(this.reqExp).append("\n");
			
			return sb.toString();
		}
	}	//	Player-class-end

	private static class Monster {
		private final String name;	//	몬스터 이름
		private final int atk;		//	몬스터 공격력
		private final int maxHp;		//	몬스터 최대 체력
		private int hp;			//	몬스터 현재 체력
		private final int def;		//	몬스터 방어력
		private final int exp;		//	처치했을때 얻을 수 있는 경험치
		private final boolean isBoss;	//	보스 몬스터일 경우 true
		
		public Monster(String name, int atk, int def, int maxHp, int exp, boolean isBoss) {
			this.name = name;
			this.atk = atk;
			this.def = def;
			this.maxHp = maxHp;
			this.hp = maxHp;
			this.exp = exp;
			this.isBoss = isBoss;
		}
	}	//	Monster-class-end

	private static class Item {
		private final String item;	//	W : 무기, A : 방어구, O : 장신구
		private final int ability;	//	아이템 종류가 W/A일 경우 무기/방어구의 공격력/방어력
		private final String sort;	//	아이템 종류가 O일 경우 장신구 종류
		
		public Item(String item, String option) {
			this.item = item;
			
			if(this.item.equals("O")) {
				this.sort = option;
				this.ability = 0;
			}
			else {
				this.ability = Integer.parseInt(option);
				this.sort = null;
			}
		}
	}	//	Item-class-end
} //	Main-class-end
```