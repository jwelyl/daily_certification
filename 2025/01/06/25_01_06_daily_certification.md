# 25_01_06_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 12100 2048 (Easy)

[12100번: 2048 (Easy)](https://boj.ma/12100/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;	//	수가 없을 경우
	private static final int MAX = 5;	//	최대 5번 이동
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;				//	보드 크기
	private static int[][] orig;		//	원본 보드
	private static int[][] copied;		//	시뮬레이션을 위해 복사한 보드
	
	private static final int[] dirs = new int[MAX];	//	5번 이동할 방향
	
	private static int answer = 0;		//	최대 5번 이동해서 얻을 수 있는 가장 큰 블록
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		orig = new int[N][N];
		copied = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				orig[y][x] = Integer.parseInt(st.nextToken());
				answer = Math.max(answer, orig[y][x]);
			}
		}
		
		permutation(0);
		
		System.out.println(answer);
	}	//	main-end
	
	private static void permutation(int nth) {
		if(nth == MAX) {	//	움직일 순서를 다 정한 경우
			copy();	//	원본 배열 복사
			move();	//	5번 이동
			
			return;
		}
		
		for(int d = 0; d < 4; d++) {
			dirs[nth] = d;
			permutation(nth + 1);
		}
	}
	
	private static void copy() {
		for(int y = 0; y < N; y++)
			for(int x = 0; x < N; x++)
				copied[y][x] = orig[y][x];
	}
	
	//	총 5번 이동
	private static void move() {
		for(int dir : dirs) {
			switch(dir) {
			case 0:
				move0();
				break;
			case 1:
				move1();
				break;
			case 2:
				move2();
				break;
			default:
				move3();
				break;
			}
		}
	}
	
	//	오른쪽으로 이동
	private static void move0() {
		for(int y = 0; y < N; y++) {	//	모든 행에 대해서
			boolean[] combined = new boolean[N];
			
			for(int repeat = 0; repeat < N; repeat++) {
				for(int x = N - 2; x >= 0; x--) {
					int cur = copied[y][x];			//	현재 블록
					int right = copied[y][x + 1];	//	오른쪽 블록
					
					if(cur == NONE)		//	현재 블록이 빈칸일 경우 skip
						continue;
					if(right == NONE) {	//	오른쪽 블록이 빈칸일 경우 현재 블록이 오른쪽 블록칸으로 그냥 이동
						copied[y][x + 1] = cur;
						copied[y][x] = NONE;
					}
					else {	//	둘 다 빈칸이 아닐 경우
						if(cur == right) {	//	둘이 같을 경우
							if(!combined[x] && !combined[x + 1]) {	//	둘다 합쳐진 블록이 아닐 경우
								copied[y][x] = NONE;
								copied[y][x + 1] *= 2;	//	현재 블록이 오른쪽 블록에 합쳐짐
								combined[x + 1] = true;	//	x + 1에 존재하는 블록은 합쳐졌음을 표시
								
								//	합쳐진 블록과 최댓값 비교
								answer = Math.max(answer, copied[y][x + 1]);
							}
						}
					}
				}
			}
		}
	}
	
	//	아래쪽으로 이동
	private static void move1() {
		for(int x = 0; x < N; x++) {	//	모든 열에 대해서
			boolean[] combined = new boolean[N];
			
			for(int repeat = 0; repeat < N; repeat++) {
				for(int y = N - 2; y >= 0; y--) {
					int cur = copied[y][x];			//	현재 블록
					int down = copied[y + 1][x];	//	아래쪽 블록
					
					if(cur == NONE)		//	현재 블록이 빈칸일 경우 skip
						continue;
					if(down == NONE) {	//	아래쪽 블록이 빈칸일 경우 현재 블록이 아래쪽 블록칸으로 그냥 이동
						copied[y + 1][x] = cur;
						copied[y][x] = NONE;
					}
					else {	//	둘 다 빈칸이 아닐 경우
						if(cur == down) {	//	둘이 같을 경우
							if(!combined[y] && !combined[y + 1]) {	//	둘다 합쳐진 블록이 아닐 경우
								copied[y][x] = NONE;
								copied[y + 1][x] *= 2;	//	현재 블록이 아래쪽 블록에 합쳐짐
								combined[y + 1] = true;	//  y + 1에 존재하는 블록은 합쳐졌음을 표시
								
								//	합쳐진 블록과 최댓값 비교
								answer = Math.max(answer, copied[y + 1][x]);
							}
						}
					}
				}
			}
		}
	}
	
	//	왼쪽으로 이동
	private static void move2() {
		for(int y = 0; y < N; y++) {	//	모든 행에 대해서
			boolean[] combined = new boolean[N];
			
			for(int repeat = 0; repeat < N; repeat++) {
				for(int x = 1; x < N; x++) {
					int cur = copied[y][x];			//	현재 블록
					int left = copied[y][x - 1];	//	왼쪽 블록
					
					if(cur == NONE)		//	현재 블록이 빈칸일 경우 skip
						continue;
					if(left == NONE) {	//	왼쪽 블록이 빈칸일 경우 현재 블록이 왼쪽 블록칸으로 그냥 이동
						copied[y][x - 1] = cur;
						copied[y][x] = NONE;
					}
					else {	//	둘 다 빈칸이 아닐 경우
						if(cur == left) {	//	둘이 같을 경우
							if(!combined[x] && !combined[x - 1]) {	//	둘다 합쳐진 블록이 아닐 경우
								copied[y][x] = NONE;
								copied[y][x - 1] *= 2;	//	현재 블록이 다음 블록에 합쳐짐
								combined[x - 1] = true;	//	x - 1에 존재하는 블록은 합쳐졌음을 표시
								
								//	합쳐진 블록과 최댓값 비교
								answer = Math.max(answer, copied[y][x - 1]);
							}
						}
					}
				}
			}
		}
	}
	
	//	위쪽으로 이동
	private static void move3() {
		for(int x = 0; x < N; x++) {	//	모든 열에 대해서
			boolean[] combined = new boolean[N];
			
			for(int repeat = 0; repeat < N; repeat++) {
				for(int y = 1; y < N; y++) {
					int cur = copied[y][x];			//	현재 블록
					int up = copied[y - 1][x];		//	위쪽 블록
					
					if(cur == NONE)		//	현재 블록이 빈칸일 경우 skip
						continue;
					if(up == NONE) {	//	위쪽 블록이 빈칸일 경우 현재 블록이 위쪽 블록칸으로 그냥 이동
						copied[y - 1][x] = cur;
						copied[y][x] = NONE;
					}
					else {	//	둘 다 빈칸이 아닐 경우
						if(cur == up) {	//	둘이 같을 경우
							if(!combined[y] && !combined[y - 1]) {	//	둘다 합쳐진 블록이 아닐 경우
								copied[y][x] = NONE;
								copied[y - 1][x] *= 2;	//	현재 블록이 위쪽 블록에 합쳐짐
								combined[y - 1] = true;	//  y - 1에 존재하는 블록은 합쳐졌음을 표시
								
								//	합쳐진 블록과 최댓값 비교
								answer = Math.max(answer, copied[y - 1][x]);
							}
						}
					}
				}
			}
		}
	}
}	//	Main-class-end
```

### **BOJ 17144** 미세먼지 안녕!

[17144번: 미세먼지 안녕!](https://boj.ma/17144/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int PURIFIER = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int R;
	private static int C;
	private static int T;
	
	private static int[][] map;
	
	private static int py1 = -1;
	private static int py2 = -1;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		map = new int[R][C];
		
		for(int r = 0; r < R; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < C; c++) {
				map[r][c] = Integer.parseInt(st.nextToken());
				if(map[r][c] == PURIFIER) {
					if(py1 == -1)
						py1 = r;
					else
						py2 = r;
				}
			}
		}
		
		for(int time = 1; time <= T; time++) {
			step1();
			step2();
		}
		
		System.out.println(answer());
	}	//	main-end
	
	//	미세먼지 확산
	private static void step1() {
		int[][] diffs = new int[R][C];	//	증감 배열, 확산이 동시에 일어나므로 임시 배열 필요
		
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				if(map[r][c] > 0) {	//	미세먼지가 있을 경우
					for(int d = 0; d < 4; d++) {
						int nr = r + dy[d];
						int nc = c + dx[d];	//	인접칸
						
						//	인접칸이 범위 안이고 공기청정기가 없을 경우
						if(isIn(nr, nc) && map[nr][nc] != PURIFIER) {
							diffs[nr][nc] += (map[r][c] / 5);	//	(nr, nc)에는 map[r][c] / 5 만큼 추가된다.
							diffs[r][c] -= (map[r][c] / 5);		//	(r, c)에는 map[r][c] / 5 만큼 빠진다.
						}
					}
				}
			}
		}
		
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++)
				map[r][c] += diffs[r][c];
		}
	}
	
	private static void step2() {
		rotateOuter(false);		//	반시계방향 회전
		rotateOuter(true);	//	시계방향 회전
	}
	
	//	부분배열의 겉껍질 회전
	private static void rotateOuter( boolean cw) {
		if(cw)  {	//	시계방향 회전
			int sy = py2;
			int sx = 0;
			int ey = R - 1;
			int ex = C - 1;
			
			for(int y = sy + 1; y <= ey - 1; y++)
				map[y][sx] = map[y + 1][sx];
			for(int x = sx; x < ex; x++)
				map[ey][x] = map[ey][x + 1];
			for(int y = ey; y > sy; y--)
				map[y][ex] = map[y - 1][ex];
			for(int x = ex; x > sx; x--)
				map[sy][x] = map[sy][x - 1];
			map[sy][sx + 1] = 0;	//정화된 공기
		}
		else {	//	반시계방향 회전
			int sy = 0;
			int sx = 0;
			int ey = py1;
			int ex = C - 1;
			
			int tmp = map[sy][sx];
			
			for(int x = sx; x < ex; x++)
				map[sy][x] = map[sy][x + 1];
			for(int y = sy; y < ey; y++)
				map[y][ex] = map[y + 1][ex];
			for(int x = ex; x > sx + 1; x--)
				map[ey][x] = map[ey][x - 1];
			map[ey][sx + 1] = 0;	//	정화된 공기
			for(int y = ey - 1; y > sy; y--)
				map[y][sx] = map[y - 1][sx];
			
			map[sy + 1][sx] = tmp;
		}
	}
	
	private static int answer() {
		int res = 2;
		
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++)
				res += map[r][c];
		}
		
		return res;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < R) && (0 <= x && x < C);
	}
}	//	Main-class-end
```