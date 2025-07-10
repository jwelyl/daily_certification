# 25_07_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17081 RPG Extreme

[17081번: RPG Extreme](http://boj.ma/17081/t)

```java
import java.io.*;
import java.util.*;

public class Main {
	static int WIN = 1;
	static int CONT = 0;
	static int LOSE = -1;

	static char EMPTY = '.';
	static char WALL = '#';
	static char ITEM = 'B';
	static char SPIKE = '^';
	static char MONSTER = '&';
	static char BOSS = 'M';
	static char PLAYER = '@';

	static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	static StringBuilder sb = new StringBuilder();
	static StringTokenizer st;

	static int N;
	static int M;
	static int K; // 몬스터 수
	static int L; // 아이템 상자 수

	static char[][] map;

	static int res = CONT;

	// monsterMap[y][x] : (y, x) 칸에 존재하는 몬스터
	static Monster[][] monsterMap;
	// itemMap[y][x] : (y, x) 칸에 존재하는 아이템
	static Item[][] itemMap;

	// 이동 명령어
	static char[] opt;

	static Player player = new Player();
	static int turn = 0;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		map = new char[N][M];
		for (int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for (int x = 0; x < M; x++) {
				if (map[y][x] == PLAYER) { // 주인공일경우
					player.sy = y;
					player.sx = x; // 주인공 최초 위치
					player.y = y;
					player.x = x; // 주인공 현재 위치
					map[y][x] = EMPTY;
				} else if (map[y][x] == MONSTER || map[y][x] == BOSS) // 몬스터일경우
					K++;
				else if (map[y][x] == ITEM) // 아이템일경우
					L++;
			}
		}
		opt = br.readLine().toCharArray();

		monsterMap = new Monster[N][M];
		for (int k = 0; k < K; k++) {
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
		for (int l = 0; l < L; l++) {
			st = new StringTokenizer(br.readLine());
			int r = Integer.parseInt(st.nextToken()) - 1;
			int c = Integer.parseInt(st.nextToken()) - 1;
			String t = st.nextToken(); // 아이템 종류
			String s = st.nextToken(); // 공격력/방어력 수치 or 장신구 종류

			itemMap[r][c] = new Item(t, s);
		}

		for (char dir : opt) {
			turn++;
			res = player.move(dir);

			if (res == WIN || res == LOSE)
				break;
		}

		for (int y = 0; y < N; y++) {
			for (int x = 0; x < M; x++) {
				if (y == player.y && x == player.x) {
					if (res != LOSE)
						sb.append(PLAYER);
					else
						sb.append(map[y][x]);
				} else
					sb.append(map[y][x]);
			}
			sb.append("\n");
		}
		sb.append("Passed Turns : ").append(turn).append("\n");

		sb.append(player);

		if (res == LOSE)
			sb.append("YOU HAVE BEEN KILLED BY ").append(player.death).append("..");
		else
			sb.append(res == WIN ? "YOU WIN!" : "Press any key to continue.");

		System.out.println(sb);
	} // main-end

	static class Player {
		int maxHp; // 주인공의 최대 체력
		int hp; // 주인공의 현재 체력

		int level; // 주인공 레벨
		int exp; // 주인공 보유 경험치
		int reqExp; // 레벨업을 위해 필요한 경험치

		int atk; // 주인공 자체 공격력
		int wAtk; // 주인공이 착용한 무기의 공격력

		int def; // 주인공 방어력
		int aDef; // 주인공이 착용한 방어구 방어력

		int sy;
		int sx; // 주인공 최초 위치
		int y;
		int x; // 주인공 현재 위치

		// 각 장신구 목록, 활성화되면 true
		Set<String> accSet;

		String death; // 죽음의 이유

		Player() {
			this.maxHp = 20;
			this.hp = maxHp;
			this.atk = 2;
			this.def = 2;
			this.level = 1;
			this.reqExp = this.level * 5;
			this.accSet = new HashSet<>();
		}

		// dir 방향으로 이동하고 그 결과를 보고함
		// 1 : 이동 결과 게임에서 승리함
		// 0 : 이동 후에도 게임이 끝나지 않고 계속됨
		// -1 : 이동 결과 게임에서 패배함
		int move(char dirCh) {
			int dir = (dirCh == 'R' ? 0 : (dirCh == 'D' ? 1 : (dirCh == 'L' ? 2 : 3)));
			int ny = y + dy[dir];
			int nx = x + dx[dir];

			if (!isIn(ny, nx) || map[ny][nx] == WALL) { // 이동하려는 칸이 범위 밖이거나 벽일 경우, 원래 칸으로 돌아가기
				if (map[this.y][this.x] == SPIKE) // 돌아가는 원래 칸이 가시일 경우
					return spike(); // 가시 밞은 결과 반환

				return CONT; // 원래 칸으로 돌아가면서 아무 일도 안생김
			} else if (map[ny][nx] == EMPTY) { // 이동하려는 칸이 빈 칸일 경우
				this.y = ny;
				this.x = nx; // 그냥 이동하기

				return CONT; // 빈칸으로 가면서 아무 일도 안생김
			} else if (map[ny][nx] == SPIKE) { // 이동하려는 칸이 가시일 경우
				this.y = ny;
				this.x = nx;

				return spike();
			} else if (map[ny][nx] == ITEM) { // 이동하려는 칸에 아이템이 있을 경우
				this.y = ny;
				this.x = nx;
				return getItem(ny, nx);
			} else // 이동하려는 칸에 몬스터가 있을 경우
				return fight(ny, nx);
		}

		int fight(int ny, int nx) {
			Monster monster = monsterMap[ny][nx];
			boolean isBoss = monster.isBoss;
			int res = CONT;

			if (accSet.contains("HU") && isBoss) // 보스전이고 HU 장신구가 있을 경우
				this.hp = this.maxHp;

			// 홀수 턴에는 주인공 공격, 짝수 턴에는 몬스터 공격
			for (int t = 1;; t++) {
				int pAtk = this.atk + this.wAtk; // 주인공이 몬스터에게 입히는 데미지
				int pDef = this.def + this.aDef; // 주인공의 실제 방어력
				int mAtk = monster.atk; // 몬스터가 주인공에게 입히는 데미지
				int mDef = monster.def; // 몬스터의 방어력

				if (t == 1 && accSet.contains("CO")) { // 첫번째 공격이고 CO 아이템이 있을 경우
					if (accSet.contains("DX")) // DX 아이템도 있을 경우
						pAtk *= 3;
					else
						pAtk *= 2;
				}

				if (t % 2 == 1) { // 주인공이 공격할 차례
					int pRealAtk = Math.max(1, pAtk - mDef); // 주인공이 몬스터에게 실제로 입히는 데미지

					if (pRealAtk >= monster.hp) { // 이번 공격에 몬스터를 잡는 경우
						this.y = ny;
						this.x = nx; // 몬스터가 있는 칸으로 이동
						monsterMap[ny][nx] = null;
						map[ny][nx] = EMPTY; // 몬스터는 사라짐

						if (accSet.contains("HR")) // HR 아이템이 있을 경우
							this.hp = Math.min(this.hp + 3, this.maxHp); // 체력 3 회복
						if (accSet.contains("EX")) // EX 아이템이 있을 경우
							this.exp += (int) (1.2 * monster.exp); // 얻는 경첨치 1.2배
						else
							this.exp += monster.exp;

						if (canLevelUp()) // 레벨업할 수 있을 경우
							levelUp();

						// 보스 몬스터를 잡는 경우 게임 승리
						res = isBoss ? WIN : CONT;
						break;
					} else
						monster.hp -= pRealAtk;
				} else { // 몬스터가 공격할 차례
					int mRealAtk = Math.max(1, mAtk - pDef); // 몬스터가 주인공에게 실제로 입히는 데미지
					if (isBoss && t == 2 && this.accSet.contains("HU")) // 보스 몬스터이고, 보스 몬스터의 첫번째 공격이고, HU 장신구가 있을 경우
						mRealAtk = 0; // 보스 몬스터의 첫번째 공격 무효화

					this.hp -= mRealAtk; // 몬스터에게 피격 당함

					if (isDead()) { // 피격당해서 죽게 될 경우
						if (canRevive()) { // 부활할 수 있을 경우
							monster.hp = monster.maxHp; // 몬스터는 최대 체력 회복함
							res = revive();
							break;
						}

						this.death = monster.name;

						res = LOSE;
						break;
					}
				}
			}

			return res;
		}

		int getItem(int ny, int nx) {
			Item item = itemMap[ny][nx];

			if (item.item.equals("O")) { // 장신구인 경우
				if (accSet.size() < 4) { // 착용한 장신구가 4개 미만일 경우
					String sort = item.sort;
					accSet.add(sort);
				}
			} else if (item.item.equals("W")) // 무기일 경우
				this.wAtk = item.ability; // 무기 교체
			else // 방어구일 경우
				this.aDef = item.ability; // 방어구 교체

			// 아이템 칸에 도착해서 아이템 연 순간 무조건 아이템은 지도에서 사라짐
			// 장신구가 이미 4개 가지고 있어서 새로운 장신구를 얻지 못했어도 사라짐
			itemMap[ny][nx] = null; // 얻은 아이템은 itemMap에서 사라짐
			map[ny][nx] = EMPTY; // 얻은 아이템은 지도에서 사라짐

			return CONT;
		}

		int spike() { // 가시를 밟을 경우
			this.hp -= this.accSet.contains("DX") ? 1 : 5; // DX 아이템이 있을 경우 1, 그렇지 않을 경우 5의 체력 감소

			if (this.isDead()) { // 가시를 밟고 죽었을 경우
				if (canRevive()) // 부활할 수 있을 경우
					return revive();

				this.death = "SPIKE TRAP";
				return LOSE;
			}

			return CONT;
		}

		// 죽게 되는지 확인
		boolean isDead() {
			return this.hp <= 0;
		}

		// 부활할 수 있는지 확인
		boolean canRevive() {
			return accSet.contains("RE");
		}

		int revive() {
			this.hp = this.maxHp; // 체력을 최대로 회복함
			this.y = this.sy;
			this.x = this.sx; // 처음 위치로 돌아감
			this.accSet.remove("RE"); // RE 아이템 소모

			return CONT;
		}

		// 레벨업 할 수 있는지 확인
		boolean canLevelUp() {
			return this.exp >= this.reqExp;
		}

		// 레벨업할 수 있을 경우 레벨업
		void levelUp() {
			this.level++; // 레벨 증가
			this.maxHp += 5; // 최대 체력 5 증가
			this.hp = this.maxHp; // 현재 체력을 최대 체력만큼 채움
			this.atk += 2; // 공격력 2 증가
			this.def += 2; // 방어력 2 증가
			this.reqExp = this.level * 5; // 다음 레벨업을 위한 경험치 설정
			this.exp = 0; // 현재 경험치 0으로 설정
		}

		static int[] dy = { 0, 1, 0, -1 };
		static int[] dx = { 1, 0, -1, 0 };

		static boolean isIn(int y, int x) {
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
	} // Player-class-end

	static class Monster {
		String name; // 몬스터 이름
		int atk; // 몬스터 공격력
		int maxHp; // 몬스터 최대 체력
		int hp; // 몬스터 현재 체력
		int def; // 몬스터 방어력
		int exp; // 처치했을때 얻을 수 있는 경험치
		boolean isBoss; // 보스 몬스터일 경우 true

		Monster(String name, int atk, int def, int maxHp, int exp, boolean isBoss) {
			this.name = name;
			this.atk = atk;
			this.def = def;
			this.maxHp = maxHp;
			this.hp = maxHp;
			this.exp = exp;
			this.isBoss = isBoss;
		}
	} // Monster-class-end

	static class Item {
		String item; // W : 무기, A : 방어구, O : 장신구
		int ability; // 아이템 종류가 W/A일 경우 무기/방어구의 공격력/방어력
		String sort; // 아이템 종류가 O일 경우 장신구 종류

		Item(String item, String option) {
			this.item = item;

			if (this.item.equals("O")) {
				this.sort = option;
				this.ability = 0;
			} else {
				this.ability = Integer.parseInt(option);
				this.sort = null;
			}
		}
	} // Item-class-end
} // Main-class-end
```