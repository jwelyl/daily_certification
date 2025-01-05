# 25_01_05_daily_certification

```
[#005 koreii] 데일리인증 20250105
1. 코딩 테스트 대비 알고리즘 학습
- Implementation, Simulation (BOJ 17143 낚시왕)
- Implementation, Simulation, BFS (BOJ 17135 캐슬 디펜스)
```

# Problem Solving (Algorithm & SQL)

### **BOJ 17143** 낚시왕

[17143번: 낚시왕](https://boj.ma/17143/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;	//	상어가 없음
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int R;
	private static int C;
	private static int M;			//	초기 상어 수 (상어 번호 : 1 ~ M)
	
	private static int[][] map;		//	map[y][x] : (y, x)에 존재하는 상어 번호
	
	private static int fisher = -1;	//	낚시왕 x 좌표
	private static int[] ypos;		//	상어 y 좌표
	private static int[] xpos;		//	상어 x 좌표
	private static int[] speeds;	//	상어 속력 정보
	private static int[] dirs;		//	상어 방향 정보
	private static int[] sizes;		//	상어 크기 정보
	private static boolean[] dead;	//	상어가 낚시왕에게 잡히거나 잡아먹혔으면 true
	
	private static int answer = 0;	//	낚시왕이 잡은 상어 크기 합
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new int[R][C];
		ypos = new int[M + 1];
		xpos = new int[M + 1];
		speeds = new int[M + 1];
		dirs = new int[M + 1];
		sizes = new int[M + 1];
		dead = new boolean[M + 1];
		
		for(int snum = 1; snum <= M; snum++) {
			st = new StringTokenizer(br.readLine());
			int r = Integer.parseInt(st.nextToken()) - 1;
			int c = Integer.parseInt(st.nextToken()) - 1;
			int s = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken()) - 1;
			int z = Integer.parseInt(st.nextToken());
			
			map[r][c] = snum;	//	(r, c)칸에 존재하는 상어 번호는 snum
			
			ypos[snum] = r;
			xpos[snum] = c;		//	snum번째 상어의 좌표는 (r, c)
			speeds[snum] = s;	//	snum번째 상어의 속력은 s
			dirs[snum] = d;		//	snum번째 상어의 방향은 d
			sizes[snum] = z;	//	snum번째 상어의 크기는 z
		}
		
		while(true) {
			if(!step1())
				break;
			step2();
			step3();
		}
		
		System.out.println(answer);
	}	//	main-end
	
	//	낚시왕이 오른쪽으로 한칸 이동
	private static boolean step1() {
		fisher++;
		
		return fisher < C;
	}
	
	//	낚시왕이 선 땅에서 가장 먼저 잡히는(y좌표가 가장 작은) 상어를 잡음
	private static void step2() {
		int y = 0;
		
		while(y < R) {
			if(map[y][fisher] != NONE) {	//	(y,	fisher)에 상어가 존재할 경우
				int snum = map[y][fisher];	//	잡힌 상어 번호
				
				dead[snum] = true;			//	해당 상어는 죽음
				map[y][fisher] = NONE;		//	(y, fisher)에는 더 이상 상어가 존재하지 않음
				answer += sizes[snum];		//	잡힌 상어 크기 answer에 추가
				break;						//	종료
			}
			
			y++;	//	더 아래에 있는 상어 찾기
		}
	}
	
	private static void step3() {
		int[][] tmp = new int[R][C];	//	동시 이동을 위한 임시 맵
		
		for(int snum = 1; snum <= M; snum++) {
			if(dead[snum])	//	해당 상어가 이미 죽었으면
				continue;

			int[] pos = move(snum);	//	snum 상어가 이동할 위치
			int y = pos[0];
			int x = pos[1];			//	snum 상어가 도착한 좌표
			
			if(tmp[y][x] != NONE) {	//	(y, x)에 도착한 또 다른 상어가 있을 경우, 둘 중 하나는 잡아먹힘
				int snum2 = tmp[y][x];	//	또 다른 상어의 번호
				
				if(sizes[snum] > sizes[snum2]) {	//	또 다른 상어보다 더 클 경우
					dead[snum2] = true;	//	또 다른 상어를 잡아먹음
					tmp[y][x] = snum;	//	(y, x)를 차지함
					ypos[snum] = y;
					xpos[snum] = x;		//	상어 위치 갱신
				}
				else	//	또 다른 상어가 더 클 경우
					dead[snum] = true;	//	또 다른 상어가 해당 상어를 잡어먹음
			}
			else {	//	다른 상어가 없을 경우
				tmp[y][x] = snum;
				ypos[snum] = y;
				xpos[snum] = x;
			}
		}
		
		map = tmp;	//	동시 이동 끝난 후 map을 갈아치움
	}
	
	private static int[] move(int snum) {
		int cy = ypos[snum];
		int cx = xpos[snum];
		int speed = speeds[snum];	//	상어 속력
		int dir = dirs[snum];		//	상어 방향
		int[] res = {cy, cx};
		
		int ny = cy;
		int nx = cx;	//	도착할 좌표
		
		if(speed != 0) {
			if(dir == 0 || dir == 1) {	//	상어가 상하로 움직일 경우, ny만 변경됨
				int q = 0;
				int r = 0;
				
				if(dir == 1) {
					r = R - 1 - cy;
					
					if(speed <= r)	//	방향 전환 없이 갈 수 있을 경우
						ny = cy + speed;
					else {
						speed -= r;
						
						q = speed / (R - 1);
						r = speed % (R - 1);
						
						if(q % 2 == 0) {
							ny = R - 1 - r;
							dirs[snum] = r == 0 ? 1 : 0;
						}
						else {
							ny = r;
							dirs[snum] = r == 0 ? 0 : 1;
						}
					}
				}
				else {
					r = cy;
					
					if(speed < r)
						ny = cy - speed;
					else {
						speed -= r;
						
						q = speed / (R - 1);
						r = speed % (R - 1);
						
						if(q % 2 == 0) {
							ny = r;
							dirs[snum] = r == 0 ? 0 : 1;
						}
						else {
							ny = R - 1 - r;
							dirs[snum] = r == 0 ? 1 : 0;
						}
					}
				}
			}
			else {	//	상어가 좌우로 움직일 경우, nx만 변경됨
				int q = 0;
				int r = 0;
				
				if(dir == 2) {
					r = C - 1 - cx;	//	dir 방향으로 갈 수 있는 칸
					
					if(speed <= r)	//	방향 전환 없이 갈 수 있을 경우
						nx = cx + speed;
					else {
						speed -= r;

						q = speed / (C - 1);
						r = speed % (C - 1);
						
						if(q % 2 == 0) {
							nx = C - 1 - r;
							dirs[snum] = r == 0 ? 2 : 3;
						}
						else {
							nx = r;
							dirs[snum] = r == 0 ? 3 : 2;
						}
					}
				}
				else {
					r = cx;
					
					if(speed < r)
						nx = cx - speed;
					else {
						speed -= r;
						
						q = speed / (C - 1);
						r = speed % (C - 1);
						
						if(q % 2 == 0) {
							nx = r;
							dirs[snum] = r == 0 ? 3 : 2;
						}
						else {
							nx = C - 1 - r;
							dirs[snum] = r == 0 ? 2 : 3;
						}
					}
				}
			}
			
			res[0] = ny;
			res[1] = nx;
		}
		
		return res;
	}
}	//	Main-class-end
```

### **BOJ 17135** 캐슬 디펜스

[17135번: 캐슬 디펜스](https://boj.ma/17135/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;	//	격자판 크기 N * M
	private static int D;	//	궁수 사거리 D
	
	private static int[][] origMap;							//	원본 격자
	private static int[][] copied;							//	시뮬레이션용 격자
	private static final int[] archers = new int[3];		//	궁수 x좌표 (y좌표는 N으로 고정) 
	
	private static int answer = 0;	//	궁수가 잡을 수 있는 적의 최대 수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		D = Integer.parseInt(st.nextToken());
		
		origMap = new int[N][M];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++)
				origMap[y][x] = Integer.parseInt(st.nextToken());
		}
		
		comb(0, 0);
		
		System.out.println(answer);
	}	//	main-end
	
	private static void copyMap() {
		copied = new int[N][M];
		
		for(int y = 0; y < N; y++)
			for(int x = 0; x < M; x++)
				copied[y][x] = origMap[y][x];
	}
	
	private static void comb(int nth, int start) {
		if(nth == 3) {		//	궁수 세 명의 위치 모두 정한 경우
			copyMap();		//	시뮬레이션을 위해 맵을 복사
			simulation();	//	시뮬레이션함
			return;
		}
		
		for(int xpos = start; xpos < M; xpos++) {
			archers[nth] = xpos;	//	nth번째 궁수의 x좌표를 xpos로 놓기
			comb(nth + 1, xpos + 1);
		}
	}
	
	private static void simulation() {
		int catched = 0;	//	궁수가 잡은 적의 수
		
		for(int ey = 0; ey < N; ey++) {
			catched += attack();
			move(ey);
		}
		
		answer = Math.max(answer, catched);
	}
	
	//	궁수 셋이 한번 공격해서 잡을 수 있는 최대 적의 수 (0 ~ 3)
	private static int attack() {
		int res = 0;
		boolean[][] catched = new boolean[N][M];				//	같은 적을 둘 이상의 궁수가 잡았을때 중복해서 계산하는 것을 방지
		int[][] catchedPos = {{-1, -1}, {-1, -1}, {-1, -1}};	//	세 궁수가 잡은 적의 좌표
		
		for(int nth = 0; nth < 3; nth++) {
			int ay = N;
			int ax = archers[nth];	//	nth번째 궁수 위치
		
			int minDist = Integer.MAX_VALUE;	//	해당 궁수로부터 가장 가까운 적의 거리
			int minY = -1;
			int minX = -1;	//	가장 가까운 적의 좌표 (잡을 수 있는 적이 없을 경우 (-1, -1))
			
			for(int dy = 1; dy <= D; dy++) {
				int xbound = D - dy;
				for(int dx = -xbound; dx <= xbound; dx++) {
					int ny = ay - dy;
					int nx = ax + dx;	//	궁수로부터 거리 D 이하인 점들중 확인할 좌표
					
					if(isIn(ny, nx) && copied[ny][nx] == 1) {	//	범위 안이고 적일 경우
						int dist = dist(ay, ax, ny, nx);
						
						if(dist < minDist) {	//	가장 가까운 적을 찾은 경우
							minDist = dist;
							minY = ny;
							minX = nx;
						}
						else if(dist == minDist && nx < minX) {	//	가장 가까운 적이 여럿일때 더 왼쪽에 있는 적을 찾은 경우
							minY = ny;
							minX = nx;
						}
					}
				}
			}
			
			catchedPos[nth][0] = minY;
			catchedPos[nth][1] = minX;	//	nth번째 궁수가 잡은 적 좌표 설정
		}
		
		//	모든 궁수가 동시에 적을 잡음
		for(int nth = 0; nth < 3; nth++) {
			int y = catchedPos[nth][0];
			int x = catchedPos[nth][1];
			
			if(y != -1 && x != -1 && !catched[y][x]) {	//	다른 궁수에 의해 잡히지 않은 적일 경우
				copied[y][x] = 0;
				catched[y][x] = true;
				res++;				//	잡음
			}
		}
		
		return res;
	}
	
	private static void move(int ey) {
		for(int x = 0; x < M; x++) {	//	x열의 적들이 내려옴
			for(int y = N - 1; y >= ey; y--) {
				if(y + 1 == N)	//	성 바로 위일 경우
					copied[y][x] = 0;
				else {
					if(copied[y][x] == 1) {
						copied[y + 1][x] = 1;
						copied[y][x] = 0;
					}
				}
			}
		}
	}
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	private static int dist(int y1, int x1, int y2, int x2) {
		return Math.abs(y1 - y2) + Math.abs(x1 - x2);
	}
}	//	Main-class-end
```