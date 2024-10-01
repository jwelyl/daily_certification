# 24_10_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16166 서울의 지하철

[](https://www.acmicpc.net/problem/16166)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Queue;
import java.util.List;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    호선 개수
    private static int[][] stations;    //    stations[i] : i호선에 존재하는 역 목록
    
    private static List<Integer>[] graph;    //    호선끼리 연결 상태 그래프
    
    private static boolean[] visited;    //    호선 방문 처리 배열
    
    private static int dst;    //    목적지 역
    
    private static Queue<int[]> q = new LinkedList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        graph = new ArrayList[N];
        for(int i = 0; i < N; i++)
            graph[i] = new ArrayList<>();
        
        visited = new boolean[N];
        
        stations = new int[N][];
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int k = Integer.parseInt(st.nextToken());
            
            stations[i] = new int[k];
            for(int j = 0; j < k; j++) {
                stations[i][j] = Integer.parseInt(st.nextToken());
                if(stations[i][j] == 0 && !visited[i]) {    //    시작역이 존재하는 호선을 큐에 넣기
                    visited[i] = true;
                    q.offer(new int[] {i, 0});
                }
            }
        }
        
        dst = Integer.parseInt(br.readLine());    //    도착역
        
        for(int i = 0; i < N - 1; i++) {    //    그래프 생성
            for(int j = i + 1; j < N; j++) {
                OUTER:
                for(int s1 : stations[i]) {
                    for(int s2 : stations[j]) {
                        if(s1 == s2) {
                            graph[i].add(j);
                            graph[j].add(i);
                            break OUTER;
                        }
                    }
                }
            }
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        while(!q.isEmpty()) {
            int[] cur = q.poll();
            int line = cur[0];    //    현재 호선
            int time = cur[1];    //    line호선까지 오는데 환승한 횟수
            
            if(isIn(line, dst))    //    line호선에 dst가 존재할 경우
                return time;
            
            for(int nline : graph[line]) {
                if(!visited[nline]) {
                    visited[nline] = true;
                    q.offer(new int[] {nline, time + 1});
                }
            }
        }
        
        return -1;    //    dst에 도달할 수 없을 경우
    }
    
    //    station 역이 line호선에 있는지 확인
    private static boolean isIn(int line, int station) {
        for(int s : stations[line]) {
            if(s == station)
                return true;
        }
        
        return false;
    }
}    //    Main-class-end
```

### BOJ 9935 문자열 폭발

[](https://www.acmicpc.net/problem/9935)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.List;
import java.util.ArrayList;
import java.util.Stack;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static char[] text;    //    문자열
    private static char[] bomb;    //    폭발 문자열
    private static char[] tmp;      //    문자열 비교를 위한 배열
    private static int len;        //    폭발 문자열 길이
    
    private static Stack<Character> stack1 = new Stack<>();
    private static Stack<Character> stack2 = new Stack<>();
    
    public static void main(String[] args) throws IOException {
        text = br.readLine().toCharArray();
        bomb = br.readLine().toCharArray();
        len = bomb.length;
        
        tmp = new char[len];
        
        for(char ch : text) {
            if(ch == bomb[len - 1]) {
            	if(stack1.size() < len -1) {
            		stack1.push(ch);
            		continue;
            	}
            	
            	int idx = len - 1;
            	stack2.push(ch);
            	tmp[idx--] = ch;
            	
            	boolean find = true;
            	for(int i = 0; i < len - 1; i++) {
            		char out = stack1.pop();
            		stack2.push(out);
            		
            		if(out != bomb[idx--]) {
            			find = false;
            			break;
            		}
            	}
            	
            	while(!stack2.isEmpty()) {
            		char out = stack2.pop();
            		if(!find)
            			stack1.push(out);
            	}
            }
            else stack1.push(ch);
        }
        
        if(stack1.isEmpty())
            sb.append("FRULA");
        else {
            List<Character> list = new ArrayList<>(stack1);
            for(char ch : list)
                sb.append(ch);
        }
        
        System.out.println(sb);
    }    //    main-end
    
    private static boolean isSame() {
        for(int i = 0; i < len; i++) {
            if(bomb[i] != tmp[i])
                return false;
        }
        
        return true;
    }
}    //    Main-class-end
```

### 삼성전자 2023년 하반기 오전 1번 왕실의 기사 대결

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/royal-knight-duel/description?page=2&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final int TRAP = 1;
	private static final int WALL = 2;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int L;	//	체스판 크기 L * L
	private static int N;	//	기사 수
	private static int Q;	//	쿼리 수
	
	private static int[][] map;				//	맵 정보
	private static int[][] knightMap;		//	knightMap[r][c] : (r, c)칸을 점유한 기사 번호
	
	private static Knight[] knights;	//	knights[i] : i번째 기사 객체
	private static int[] initialHp;		//	initialHp[i] : i번째 기사의 최초 체력
	
	private static int ans = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		L = Integer.parseInt(st.nextToken());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		map = new int[L + 2][L + 2];
		knightMap = new int[L + 2][L + 2];
		for(int r = 0; r <= L + 1; r++)
			Arrays.fill(map[r], WALL);
		
		for(int r = 1; r <= L; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 1; c <= L; c++)
				map[r][c] = Integer.parseInt(st.nextToken());
		}
		
		knights = new Knight[N + 1];
		initialHp = new int[N + 1];
		for(int num = 1; num <= N; num++) {
			st = new StringTokenizer(br.readLine());
			int r = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			int h = Integer.parseInt(st.nextToken());
			int w = Integer.parseInt(st.nextToken());
			int k = Integer.parseInt(st.nextToken());
		
			knights[num] = new Knight(r, c, h, w, k);
			initialHp[num] = k;
		}
		
		drawKnight();
		
		for(int q = 1; q <= Q; q++) {
			st = new StringTokenizer(br.readLine());
			int num = Integer.parseInt(st.nextToken());	//	움직일 기사 번호
			int dir = Integer.parseInt(st.nextToken());	//	움직일 방향
			
			Set<Integer> movedKnights = new HashSet<>();	//	움직여야 하는 기사 번호 집합
			if(knights[num].isAlive()) {	//	해당 기사가 아직 살아있을 경우
				if(checkMove(num, dir, movedKnights)) {		//	해당 기사를 이동시킬 수 있을 경우
					for(int movedKnightNum : movedKnights)
						move(movedKnightNum, dir, num == movedKnightNum);	//	이동시킴
					drawKnight();
				}
			}
		}
		
		for(int num = 1; num <= N; num++) {
			if(knights[num].isAlive())	//	살아있는 기사의 경우
				ans += initialHp[num] - knights[num].hp;	//	받은 데미지를 더함
		}
		
		System.out.println(ans);
	}	//	main-end
	
	private static void move(int num, int dir, boolean moved) {
		Knight knight = knights[num];
		int row = knight.row;
		int col = knight.col;
		final int h = knight.h;
		final int w = knight.w;
		
		int nRow = row + dy[dir];
		int nCol = col + dx[dir];
		
		int minus = 0;	//	num번째 기사가 이동했을 때 새로 차지하는 h * w칸에 존재하는 함정 개수
		
		for(int r = nRow; r < nRow + h; r++) {
			for(int c = nCol; c < nCol + w; c++) {
				if(map[r][c] == TRAP)
					minus++;
			}
		}
		
		knight.row = nRow;
		knight.col = nCol;
		if(!moved)	//	직접 이동한게 아니라 밀린 경우
			knight.hp -= minus;
	}
	
	private static boolean checkMove(int num, int dir, Set<Integer> movedKnightSet) {
		Knight knight = knights[num];
		int row = knight.row;
		int col = knight.col;
		final int h = knight.h;
		final int w = knight.w;
		
		movedKnightSet.add(num);	//	이동시켜야 할 기사 번호에 추가
		
		int[][] nextPosArr = null;	//	num번째 기사가 움직이면서 새로 차지하게 될 칸들의 배열
		
		if(dir == 0) {	//	위쪽으로 이동할 경우
			nextPosArr = new int[w][2];
			for(int i = 0; i < w; i++) {
				nextPosArr[i][0] = row - 1;
				nextPosArr[i][1] = col + i;
			}
			
		}
		else if(dir == 1) {
			nextPosArr = new int[h][2];
			for(int i = 0; i < h; i++) {
				nextPosArr[i][0] = row + i;
				nextPosArr[i][1] = col + w;
			}
				
		}
		else if(dir == 2) {	//	아래쪽으로 이동할 경우
			nextPosArr = new int[w][2];
			for(int i = 0; i < w; i++) {
				nextPosArr[i][0] = row + h;
				nextPosArr[i][1] = col + i;
			}
		}
		else {
			nextPosArr = new int[h][2];
			for(int i = 0; i < h; i++) {
				nextPosArr[i][0] = row + i;
				nextPosArr[i][1] = col - 1;
			}
		}
		
		for(int i = 0; i < nextPosArr.length; i++) {
			int y = nextPosArr[i][0];
			int x = nextPosArr[i][1];	//	현재 기사를 이동시켰을 때 차지하게 될 칸 (y, x)
			
			if(map[y][x] == WALL)	//	벽에 가로막힐 경우
				return false;	//	기사를 밀 수 없음
			
			if(knightMap[y][x] != 0) {	//	다른 기사가 있을 경우
				if(!checkMove(knightMap[y][x], dir, movedKnightSet))	//	다른 기사를 밀 수 없을 경우
					return false;
			}
		}

		return true;
	}
	
	private static void drawKnight() {
		for(int r = 1; r <= L; r++)
			for(int c = 1; c <= L; c++)
				knightMap[r][c] = 0;
		
		for(int num = 1; num <= N; num++)
			drawKnight(num);
	}
	
	//	num번째 기사를 기사 배치도에 배치함
	private static void drawKnight(int num) {
		if(knights[num].isAlive()) {	//	해당 기사가 살아있을 경우
			int row = knights[num].row;
			int col = knights[num].col;
			int h = knights[num].h;
			int w = knights[num].w;
			
			for(int r = row; r < row + h; r++) {
				for(int c = col; c < col + w; c++)
					knightMap[r][c] = num;
			}
		}
	}

	private static final int[] dy = {-1, 0, 1, 0};
	private static final int[] dx = {0, 1, 0, -1};
	
	private static class Knight {
		public int row;
		public int col;
		public final int h;
		public final int w;
		public int hp;
		
		public Knight(int r, int c, int h, int w, int k) {
			this.row = r;
			this.col = c;
			this.h = h;
			this.w = w;
			this.hp = k;
		}
		
		public boolean isAlive() {
			return this.hp > 0;
		}
	}
}	//	Main-class-end
```

### 삼성전자 2023년 하반기 오후 1번 루돌프의 반란

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/rudolph-rebellion/description?page=2&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	게임판 크기
	private static int M;	//	게임 턴 수
	private static int P;	//	산타 수
	private static int C;	//	루돌프 힘
	private static int D;	//	산타의 힘
	
	private static int ry = 0;
	private static int rx = 0;	//	루돌프 위치
	
	private static int[][] santaMap;	//	santaMap[r][c] : (r, c)칸에 존재한느 산타 번호
	
	private static Santa[] santas;
	private static int deadCnt = 0;	//	죽은 산타 수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		P = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		D = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		ry = Integer.parseInt(st.nextToken());
		rx = Integer.parseInt(st.nextToken());
		
		santaMap = new int[N + 1][N + 1];
		santas = new Santa[P + 1];
		
		for(int p = 1; p <= P; p++) {
			st = new StringTokenizer(br.readLine());
			int num = Integer.parseInt(st.nextToken());
			int sy = Integer.parseInt(st.nextToken());
			int sx = Integer.parseInt(st.nextToken());
			
			santaMap[sy][sx] = num;
			santas[num] = new Santa(sy, sx);
		}
		
		for(int time = 1; time <= M; time++) {
			rudolphMove(time);
			if(isEnd())
				break;
			santaMove(time);
			if(isEnd())
				break;
			addScores();
		}
		
		for(int p = 1; p <= P; p++)
			sb.append(santas[p].score).append(" ");
		
		System.out.print(sb);
	}	//	main-end
	
	private static void rudolphMove(int time) {
		int minDist = Integer.MAX_VALUE;	//	가장 가까운 산타까지 거리
		int maxR = 0;
		int maxC = 0;		//	산타 좌표
		Santa santa = null;	//	가장 가까운 산타
		
		for(int p = 1; p <= P; p++) {
			santa = santas[p];	//	p번째 산타
			
			if(!santa.isAlive())	//	이미 죽은 산타면 skip
				continue;
			
			int r = santa.y;
			int c = santa.x;	//	산타 좌표
			
			int dist = dist(ry, rx, r, c);
			
			if(dist < minDist) {	//	1. 해당 산타가 가장 가까울 경우
				minDist = dist;
				maxR = r;
				maxC = c;
			}
			else if(dist == minDist) {	//	1을 만족하는 산타가 여럿일 경우
				if(r > maxR) {	//	2. 행이 더 큰 산타 선택
					maxR = r;
					maxC = c;
				}
				else if(r == maxR && c > maxC)	//	1, 2를 만족하는 산타가 여럿일 경우, 열이 더 큰 산타 선택
					maxC = c;
			}
		}
		
		minDist = Integer.MAX_VALUE;
		int nry = 0;
		int nrx = 0;	//	루돌프가 이동할 칸
		int dir = 0;
		
		for(int d = 0; d < 8; d++) {
			int ny = ry + dy[d];
			int nx = rx + dx[d];
			
			if(!isIn(ny, nx))	//	범위를 벗어난 경우
				continue;
			
			int dist = dist(ny, nx, maxR, maxC);
			
			if(dist < minDist) {
				minDist = dist;
				nry = ny;
				nrx = nx;
				dir = d;
			}
		}
		
		int num = santaMap[nry][nrx];
		
		if(num != 0 && santas[num].isAlive())
			collision(num, C, dir, time);	//	루돌프가 이동해서 num 산타와 충돌
		
		ry = nry;
		rx = nrx;	//	루돌프 위치 갱신
	}
	
	private static void santaMove(int time) {
		for(int p = 1; p <= P; p++) {
			Santa santa = santas[p];
			
			//	이미 죽거나 기절한 산타는 이동하지 않음
			if(!santa.isAlive() || santa.stunned >= time)
				continue;
			
			int cy = santa.y;
			int cx = santa.x;
			
			int minDist = dist(cy, cx, ry, rx);	//	현재 루돌프까지의 거리
			int minY = -1;
			int minX = -1;	//	산타가 이동할 칸
			int dir = -1;	//	산타가 이동할 방향
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(!isIn(ny, nx) || (santaMap[ny][nx] != 0 && santas[santaMap[ny][nx]].isAlive()))	//	범위를 벗어나거나 다른 산타가 있을 경우
					continue;
				
				int dist = dist(ny, nx, ry, rx);
				
				if(dist < minDist) {	//	거리가 더 가까워질 경우
					minDist = dist;
					minY = ny;
					minX = nx;
					dir = d;
				}
			}
			
			if(minY != -1 && minX != -1) {	//	산타가 이동할 수 있을 경우
				santa.y = minY;
				santa.x = minX;							//	산타 좌표 재설정
				santaMap[cy][cx] = 0;					//	산타 원래 있던 칸에서 지우기
				santaMap[santa.y][santa.x] = p;			//	산타 새로 이동할 칸에 그리기
				
				if(santa.y == ry && santa.x == rx)	//	루돌프가 있는 칸일 경우
					collision(p, D, reverseDir(dir), time);	//	p 산타가 이동해서 루돌프와 충돌
			}
		}
	}
	
	private static void collision(int num, int add, int dir, int time) {
		santas[num].score += add;		//	점수 추가
		santas[num].stunned = time + 1;	//	time에 루돌프와 충돌하여 time + 1까지 기절 상태
	
		int cy = santas[num].y;
		int cx = santas[num].x;			//	산타 현재 위치
		int ny = cy + dy[dir] * add;
		int nx = cx + dx[dir] * add;	//	산타가 날아갈 위치
		
		santas[num].y = ny;
		santas[num].x = nx;	//	해당 방향으로 날아감
		
		santaMap[cy][cx] = 0;	//	기존에 있던 칸에서 산타 지우기
		if(!isIn(ny, nx)) {	//	범위를 벗어날 경우
			santas[num].dead = true;	//	해당 산타 죽이기
			return;
		}
		
		int nnum = santaMap[ny][nx];	//	(ny, nx)칸 번호
		santaMap[ny][nx] = num;	//	해당 칸에 현재 산타가 자리잡음
		
		if(nnum != 0)	//	해당 칸에 산타가 있을 경우
			interaction(nnum, dir);	//	상호작용 발생
 	}
	
	private static void interaction(int num, int dir) {
		Santa santa = santas[num];	//	dir 방향으로 한 칸 밀려나게 된 산타
		int cy = santa.y;
		int cx = santa.x;	//	해당 산타 원래 위치
		
		int ny = cy + dy[dir];
		int nx = cx + dx[dir];	//	해당 산타가 밀려날 칸
		
		santa.y = ny;
		santa.x = nx;
		
		if(!isIn(ny, nx)) {	//	밀려난 칸이 범위 밖일 경우
			santa.dead = true;	//	해당 산타는 죽음
			return;
		}
		
		int nnum = santaMap[ny][nx];	//	밀려난 칸에 원래 있던 산타
		
		santaMap[ny][nx] = num;	//	해당 산타는 밀려난 칸으로 이동
		
		if(nnum != 0)	//	밀려난 칸에 원래 산타가 있을 경우
			interaction(nnum, dir);	//	그 산타도 밀려남
	}
	
	private static void addScores() {
		for(int p = 1; p <= P; p++) {
			if(santas[p].isAlive())
				santas[p].score++;
		}
	}
	
	private static boolean isEnd() {
		return deadCnt == P;
	}
	
	private static int reverseDir(int dir) {
		if(dir == 0 || dir == 2)
			return 2 - dir;
		return 4 - dir;
	}

	private static final int[] dy = {-1, 0, 1, 0, -1, 1, 1, -1};
	private static final int[] dx = {0, 1, 0, -1, 1, 1, -1, -1};
	
	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= N) && (1 <= x && x <= N);
	}
	
	private static int dist(int y1, int x1, int y2, int x2) {
		return (y1 - y2) * (y1 - y2) + (x1 - x2) * (x1 - x2);
	}
	
	private static class Santa {
		public int y;
		public int x;			//	산타 위치
		public int score;		//	해당 산타 점수
		public boolean dead;	//	산타가 죽었으면 true
		public int stunned;		//	기절 유효 시간 (k에 기절했다면 k + 1까지 유효)
		
		public Santa(int y, int x) {
			this.y = y;
			this.x = x;
			this.score = 0;
			this.dead = false;
			this.stunned = -1;
		}
		
		public boolean isAlive() {
			return !this.dead;
		}
	}
}	//	Main-class-end
```

### 삼성전자 2024년 상반기 오후 1번 마법의 숲 탐색

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/magical-forest-exploration/description?page=1&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int R;
	private static int C;	//	숲 크기 R * C
	private static int K;	//	정령 수
	
	private static int[][] map;	//	map[r][c] : (r, c)를 차지한 골렘 번호, EXIT일 경우, 출구임
	private static boolean[][] exitMap;	//	exitMap[r][c] : (r, c)칸에 출구가 존재하면 true
	
	private static int sum = 0;	//	각 정렬들이 최종적으로 위치한 행의 총합
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		map = new int[R + 4][C + 1];
		exitMap = new boolean[R + 4][C + 1];
		
		for(int k = 1; k <= K; k++) {
			st = new StringTokenizer(br.readLine());
			int r = 2;
			int c = Integer.parseInt(st.nextToken());
			int dir = Integer.parseInt(st.nextToken());
			
			new Golem(k, r, c, dir).move();
		}
		
		System.out.println(sum);
	}	//	main-end
	
	private static final int[] dr = {-1, 0, 1, 0};
	private static final int[] dc = {0, 1, 0, -1};
	
	private static boolean isIn(int r, int c) {
		return (1 <= r && r <= R + 3) && (1 <= c && c <= C);
	}
	
	private static class Golem {
		public final int num;		//	골렘 번호
		public int r;
		public int c;	//	골렘 위치
		public int dir;	//	골렘 방향
		
		public Golem(int num, int r, int c, int dir) {
			this.num = num;
			this.r = r;
			this.c = c;
			this.dir = dir;
		}
		
		public int[] getExit() {
			return new int[] {this.r + dr[this.dir], this.c + dc[this.dir]};
		}
		
		public boolean isIn() {	//	해당 골렘이 숲 안에 있으면 true
			for(int d = 0; d < 4; d++) {
				int sr = r + dr[d];
				int sc = c + dc[d];
				
				if(!(4 <= sr && sr <= R + 3) || !(1 <= sc && sc <= C))
					return false;
			}
			
			return true;
		}

		public void removeAll() {
			for(int r = 1; r <= R + 3; r++) {
				Arrays.fill(map[r], 0);
				Arrays.fill(exitMap[r], false);
			}
		}
		
		public void draw() {
			int[] exit = this.getExit();
			
			map[this.r][this.c] = this.num;
			
			for(int d = 0; d < 4; d++) {
				map[this.r + dr[d]][this.c + dc[d]] = this.num;
				
				if(this.r + dr[d] == exit[0] && this.c + dc[d] == exit[1])
					exitMap[this.r + dr[d]][this.c + dc[d]] = true;
			}
		}
		
		//	1. 남쪽 방향으로 내려갈 수 있는지 확인
		public boolean checkSouth() {
			if(!Main.isIn(r + 1, c - 1) || map[r + 1][c - 1] != 0)
				return false;
			if(!Main.isIn(r + 2, c) || map[r + 2][c] != 0)
				return false;
			if(!Main.isIn(r + 1, c + 1) || map[r + 1][c + 1] != 0)
				return false;

			return true;
		}
		
		//	1. 남쪽으로 이동하기 (dir 변경 없음)
		public void moveSouth() {
			this.r++;	//	중심 좌표 1 증가
		}
		
		//	2. 서쪽 방향으로 이동 후 내려갈 수 있는지 확인
		public boolean checkWest() {
			if(!Main.isIn(r - 1, c - 1) || map[r - 1][c - 1] != 0)
				return false;
			if(!Main.isIn(r, c - 2) || map[r][c - 2] != 0)
				return false;
			if(!Main.isIn(r + 1, c - 2) || map[r + 1][c - 2] != 0)
				return false;
			if(!Main.isIn(r + 1, c - 1) || map[r + 1][c - 1] != 0)
				return false;
			if(!Main.isIn(r + 2, c - 1) || map[r + 2][c - 1] != 0)
				return false;
			
			return true;
		}
		
		//	1. 서쪽으로 이동하고 내려가기 (dir 변경)
		public void moveWest() {
			this.dir = (this.dir == 0) ? 3 : this.dir - 1;
			
			this.r++;
			this.c--;
		}
		
		//		3. 동쪽 방향으로 이동 후 내려갈 수 있는지 확인
		public boolean checkEast() {
			if(!Main.isIn(r - 1, c + 1) || map[r - 1][c + 1] != 0)
				return false;
			if(!Main.isIn(r, c + 2) || map[r][c + 2] != 0)
				return false;
			if(!Main.isIn(r + 1, c + 2) || map[r + 1][c + 2] != 0)
				return false;
			if(!Main.isIn(r + 1, c + 1) || map[r + 1][c + 1] != 0)
				return false;
			if(!Main.isIn(r + 2, c + 1) || map[r + 2][c + 1] != 0)
				return false;
			
			return true;
		}
		
		//	1. 동쪽으로 이동하고 내려가기 (dir 변경)
		public void moveEast() {
			this.dir = (this.dir + 1) % 4;
			
			this.r++;
			this.c++;
		}
		
		public void move() {
			while(true) {
				if(checkSouth()) {		//	1. 남쪽으로 내려갈 수 있으면
					moveSouth();		//	남쪽으로 내려가기
					continue;
				}
				else if(checkWest()) {	//	2. 남쪽으로 내려갈 수 없고, 서쪽으로 내려갈 수 있으면
					moveWest();			//	서쪽으로 내려가기
					continue;
				}
				else if(checkEast()) {	//	3. 서쪽으로도 내려갈 수 없고, 동쪽으로 내려갈 수 있으면
					moveEast();			//	동쪽으로 내려가기
					continue;
				}
				
				break;	//	남, 서, 동 셋 다 불가능하면 이동 종료
			}
			
			if(!this.isIn())	//	만약 이동을 끝냈는데 골렘 일부가 숲 밖에 있으면
				this.removeAll();	//	숲에 있던 모든 골렘 지우기
			else {	//	이동을 끝내고 골렘이 숲 안에 완전히 있으면
				this.draw();	//	골렘을 map에 그리기
				Main.sum += this.bfs();	//	정령을 이동시켰을 때 최종 행 위치 구하기
			}
		}
		
		private int bfs() {
			Queue<int[]> q = new LinkedList<>();
			boolean[][] visited = new boolean[R + 4][C + 1];
			int maxRow = this.r;
			
			visited[this.r][this.c] = true;
			q.offer(new int[] {this.r, this.c, this.num});
			
			while(!q.isEmpty()) {
				int[] cur = q.poll();
				int cr = cur[0];
				int cc = cur[1];
				int cnum = cur[2];
				
				for(int d = 0; d < 4; d++) {
					int nr = cr + dr[d];
					int nc = cc + dc[d];
					
					if(!Main.isIn(nr, nc) || visited[nr][nc])	//	범위 밖이거나 이미 방문한 칸일 경우
						continue;
					
					if(exitMap[cr][cc]) {	//	현재 칸이 출구일 경우
						if(map[nr][nc] != 0) {	//	다음 칸이 빈 칸만 아니면 갈 수 있음
							maxRow = Math.max(maxRow, nr);
							visited[nr][nc] = true;
							q.offer(new int[] {nr, nc, map[nr][nc]});
						}
					}
					else {	//	현재 칸이 출구가 아닐 경우
						if(map[nr][nc] == cnum) {	//	다음 칸이 같은 골렘 칸일경우 갈 수 있음
							maxRow = Math.max(maxRow, nr);
							visited[nr][nc] = true;
							q.offer(new int[] {nr, nc, map[nr][nc]});
						}
					}
				}
			}
			
			return maxRow - 3;
		}
	}	//	Golem-class-end
}	//	Main-class-end
```