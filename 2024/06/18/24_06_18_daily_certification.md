# 24_06_18_daily_certification

# Database

## Relational Database vs NoSQL

Q. RDB 대비 NoSQL의 장점이 뭔가?

A1. 비정형데이터를 처리하는데 유리하다.

A2. ACID 원칙을 어느 정도 포기하는 대신 처리량이 높다.

A3. 정규화를 포기하는 대신 JOIN 연산이 필요 없어서 데이터 조회가 빠르다.

Q. Foreign Key Constraint를 걸지 않는 이유가 뭔가?

A1. FK Constraint 자체가 성능을 저하시킨다.

A2. FK Constraint에 의해 데이터 삽입, 수정, 삭제 등이 어려워 데이터베이스 관리가 까다롭다.

A3. 최근 개발 트렌드는 DBMS에서의 FK Constraint를 지양하고 Application Level에서 무결성을 검증한다 고한다.

Q. DB Index가 무엇인가?

A. 데이터베이스에서 조회 성능을 높이기 위해 생성하는 자료구조로 DB 테이블의 특정 컬럼으로 데이터를 조회할 때 해당 데이터를 빠르게 찾기 위해 사용됩니다.

Q. DB Index로 어떤 자료구조를 사용하는가? 조회의 시간 복잡도는 어떻게 되는가?

A. 주로 B-Tree 계열 트리를 사용한다. 조회의 경우 N개의 데이터에서 특정 값의 데이터를 찾는데 O(logN)의 시간이 걸린다.

Q. HashTable 자료구조의 조회의 시간복잡도는 어떤가? 왜 HashTable을 DB Index로 사용하지 않는가?

A. HashTable의 조회의 시간 복잡도는 O(1)이지만, B-Tree Index와 다르게 데이터가 정렬된 형태로 존재하지 않으므로 범위 탐색과 같은 기능을 제공하지 않는다. 반면 B-Tree 계열 Index는 데이터가 정렬된 형태로 존재하므로 범위 검색과 같은 다양한 쿼리를 일관된 성능으로 제공한다.

# .Problem Solving (Algorithm & SQL)

### BOJ 8972 미친 아두이노

[](https://www.acmicpc.net/problem/8972)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = -1;	//	아무것도 없는 칸
	private static final int MINE =  0;	//	내 아두이노 번호
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static List<Integer>[][] board;	//	보드
	
	private static int R;	//	보드 세로 크기
	private static int C;	//	보드 가로 크기
	
	private static char[] cmdList;
	
	private static Arduino mine;	//	내 아두이노
	
	private static int num = 1;		//	미친 아두이노 번호
	private static final List<Arduino> arduinos = new ArrayList<>();	//	미친 아두이노 목록
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		R = Integer.parseInt(tokens.nextToken());
		C = Integer.parseInt(tokens.nextToken());
		
		board = new Stack[R][C];
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				board[r][c] = new Stack<>();
				board[r][c].add(NONE);
			}
		}
		
		for(int r = 0; r < R; r++) {
			String input = br.readLine();
			for(int c = 0; c < C; c++) {
				char ch = input.charAt(c);
				
				if(ch == 'I') {
					mine = new Arduino(MINE, r, c);
					board[r][c].add(MINE);
				}
				else if(ch == 'R') {
					Arduino arduino = new Arduino(num, r, c);
					arduinos.add(arduino);
					board[r][c].add(num++);
				}
			}
		}
		
		cmdList = br.readLine().toCharArray();
		
		for(int step = 1; step <= cmdList.length; step++) {
			if(!(step12(cmdList[step - 1]) && step34())) {
				System.out.println("kraj " + step);
				return;
			}
			step5();
		}
		
		printBoard();
	}	//	main-end
	
	//	step 1 ~ 2
	//	내 아두이노가 주어진 방향으로 이동함
	//	만약 미친 아두이노가 있는 칸으로 가게 되면 false 반환
	//	그 외에는 위치 갱신하고 true 반환
	private static boolean step12(char cmd) {
		int dir = (cmd - '0');	//	이동하려는 방향
		
		int ny = mine.y + dy[dir];
		int nx = mine.x + dx[dir];	//	이동하려는 칸 
		
		if(board[ny][nx].get(board[ny][nx].size() - 1) > MINE)	//	미친 아두이노가 있는 칸일 경우
			return false;
		
		board[ny][nx].add(MINE);
		board[mine.y][mine.x].remove(Integer.valueOf(MINE));
		mine.y = ny;
		mine.x = nx;	//	내 아두이노 위치 갱신 
		
		return true;
	}
	
	//	step 3 ~ 4
	//	미친 아두이노가 내 아두이노와 가까워지는 방향으로 이동함
	//	미친 아두이노 중 하나라도 내 아두이노와 충돌하면 false 반환
	//	그 외에는 true 반환
	private static boolean step34() {
		for(Arduino arduino : arduinos) {
			if(!arduino.move())	//	이 미친 아두이노가 내 아두이노와 충돌할 경우
				return false;
		}
		
		return true;
	}
	
	//	step 5
	//	어느 한 칸에 미친 아두이노가 두 개 이상 있을 경우 해당 칸의 미친 아두이노를 모두 무력화함
	private static void step5() {
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int state = board[r][c].get(board[r][c].size() - 1);
				
				if(state == NONE || state == MINE)
					continue;
				
				if(board[r][c].size() >= 3) {	//	NONE을 제외하고 2개 이상 있다는 것은 미친 아두이노가 2개 이상 있다는 것임
					for(int i = board[r][c].size() - 1; i > 0; i--) {
						int num = board[r][c].remove(i);	//	해당 칸에 있는 미친 아두이노 제거
						arduinos.get(num - 1).disabled = true;	//	무력화시키기
					}
				}
			}
		}
	}
	
	private static void printBoard() {
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int state = board[r][c].get(board[r][c].size() - 1);
				
				sb.append(state == NONE ? '.' : (state == MINE ? 'I' : 'R'));
			}
			sb.append("\n");
		}
		
		System.out.print(sb);
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, 0, 0, -1, -1, -1};
	private static final int[] dx = {0, -1, 0, 1, -1, 0, 1, -1, 0, 1};
	
	public static boolean isIn(int y, int x) {
		return (0 <= y && y < R) && (0 <= x && x < C);
	}
	
	private static class Arduino {
		final int num;	//	아두이노 번호 (내껀 0번, 나머지는 1번 부터)
		int y;
		int x;		//	아두이노 위치
		boolean disabled = false;	//	두 개 이상의 미친 아두이노가 같은 칸에 있으면 같은 칸에 있는 모든 아두이노가 무력화됨
		
		public Arduino(int num, int y, int x) {
			this.num = num;
			this.y = y;
			this.x = x;
		}
		
		//	8방향 중 내 아두이노와 가까워지는 방향으로 이동
		//	이미 무력화된 아두이노면 true 반환
		//	가장 가까워지는 방향으로 이동했는데 내 아두이노와 만나면 false 반환
		//	그 외에는 true 반환
		public boolean move() {
			if(this.disabled)
				return true;
			
			int minDist = Integer.MAX_VALUE;	//	내 아두이노와 가장 가까운 거리
			int minDir = -1;					//	그 때 이동방향
			int ny = 0;
			int nx = 0;
	
			for(int d = 1; d <= 9; d++) {
				ny = this.y + dy[d];
				nx = this.x + dx[d];
				
				if(d == 5 || !isIn(ny, nx))	//	제자리에 있거나 범위를 벗어날 경우
					continue;
				
				int dist = manhattanDist(ny, nx);
				
				if(dist < minDist) {
					minDist = dist;
					minDir = d;
				}
			}

			ny = this.y + dy[minDir];
			nx = this.x + dx[minDir];
			
			if(board[ny][nx].get(board[ny][nx].size() - 1) == MINE)	//	내 아두이노와 미친 아두이노가 부딪힐 경우
				return false;
			
			board[this.y][this.x].remove(Integer.valueOf(this.num));	//	현재 위치에서 미친 아두이노 제거
			board[ny][nx].add(this.num);	//	이동할 위치로 미친 아두이노 이동
			
			this.y = ny;
			this.x = nx;	//	미친 아두이노 위치 갱신
			
			return true;
		}
		
		private int manhattanDist(int ny, int nx) {
			return Math.abs(ny - mine.y) + Math.abs(nx - mine.x);
		}
	}
}	//	Main-class-end
```