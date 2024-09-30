# 24_09_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13144 List of Unique Numbers

[](https://www.acmicpc.net/problem/13144)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 100_001;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정수 개수
    
    private static int[] nums;
    
    private static int start = 0;
    private static int end = 0;        //    두 포인터
    
    private static final int[] cnts = new int[MAX];    //    cnts[num] : [start, end]에 num이 포함된 개수
    
    private static long ans = 0L;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        nums = new int[N];
        st = new StringTokenizer(br.readLine());
        
        for(int i = 0; i < N; i++)
            nums[i] = Integer.parseInt(st.nextToken());
        
        while(end < N) {
            int num = nums[end];
            
            if(cnts[num] == 0) {    //    [start, end]까지 모든 수가 한 번 이하로만 등장할 경우
                cnts[num] = 1;
                ans += (end - start + 1);
                end++;
            }
            else {    //    중복된 수가 등장할 경우
                int dup = num;    //    중복된 수
            
                while(start < end) {
                    cnts[nums[start]]--;    //    start에 있던 수를 1개 줄임
                    start++;                //    start 1 증가
                    
                    if(cnts[dup] == 0)    //    중복이 사라진 경우
                        break;
                }
            }
        }

        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 19236 **청소년 상어**

[](https://www.acmicpc.net/problem/19236)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int SIZE = 4;
	
	private static final int NONE = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	//	origMap[r][c] : (r, c)칸에 있는 물고기 번호, 물고기 없으면 NONE (원본)
	private static final int[][] origMap = new int[SIZE][SIZE];
	
	//	origFishPos[nth] : nth번째 물고기의 위치 {y, x}
	private static final int[][] origFishPos = new int[SIZE * SIZE + 1][2];
	//	origFishDir[nth] : nth번째 물고기의 방향
	private static final int[] origFishDir = new int[SIZE * SIZE + 1];
	
	private static int maxEat = 0;	//	상어가 먹을 수 있는 물고기 번호 합의 최댓값
	
	public static void main(String[] args) throws IOException {
		for(int r = 0; r < SIZE; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < SIZE; c++) {
				//	(r, c)칸 정보
				int num = Integer.parseInt(st.nextToken());			//	물고기 번호
				int dir = Integer.parseInt(st.nextToken()) - 1;		//	물고기 방향
				
				origMap[r][c] = num;	//	(r, c)에는 num 물고기가 있음
				origFishPos[num][0] = r;
				origFishPos[num][1] = c;	//	num 물고기 좌표는 (r, c)임
				origFishDir[num] = dir;		//	num 물고기는 dir 방향 가지고 있음
			}
		}
		
		//	(0, 0)의 물고기를 상어가 잡아먹음
		int sharkY = 0;
		int sharkX = 0;		//	상어 좌표
		int sharkDir = 0;	//	상어 방향
		int eat = origMap[sharkY][sharkX];	//	상어가 그 동안 잡아먹은 물고기 번호 합
		sharkDir = origFishDir[eat];		//	상어가 잡아먹은 물고기의 방향
		
		origMap[sharkY][sharkX] = NONE;	//	(0, 0)에는 더이상 물고기가 없음
		origFishPos[eat][0] = NONE;
		origFishPos[eat][1] = NONE;	//	잡아먹힌 물고기는 좌표가 더 이상 없음
		origFishDir[eat] = NONE;	//	잡아먹힌 물고기는 방도 없음
		
		backtracking(sharkY, sharkX, sharkDir, eat, origMap, origFishPos, origFishDir);
		
		System.out.println(maxEat);
	}	//	main-end
	
	private static void backtracking(int sharkY, int sharkX, int sharkDir, int eat, int[][] map, int[][] fishPos, int[] fishDir) {
		maxEat = Math.max(maxEat, eat);
		
		int[][] nMap = copyArr(map);
		int[][] nFishPos = copyArr(fishPos);
		int[] nFishDir = copyArr(fishDir);
		
		//	모든 물고기 이동
		moves(sharkY, sharkX, nMap, nFishPos, nFishDir);
		
		for(int d = 1; d <= SIZE; d++) {
			int nSharkY = sharkY + dy[sharkDir] * d;
			int nSharkX = sharkX + dx[sharkDir] * d;
			int nSharkDir = sharkDir;
			int nEat = eat;
			
			if(!isIn(nSharkY, nSharkX))	//	범위를 벗어날 경우, 해당 방향으로 갈 수 있는 칸은 더 이상 없음
				break;
			
			if(nMap[nSharkY][nSharkX] != NONE) {	//	해당 칸에 물고기가 있을 경우
				int num = nMap[nSharkY][nSharkX];	//	잡아먹을 물고기 번호
				int y = nFishPos[num][0];
				int x = nFishPos[num][1];			//	잡아먹을 물고기 좌표
				int dir = nFishDir[num];			//	잡아먹을 물고기 방향
				
				nSharkDir = dir;			//	잡아먹은 물고기의 방향을 가짐
				nEat += num;				//	잡아먹은 물고기 번호 추가
				
				nMap[y][x] = NONE;			//	잡아먹은 물고기 맵에서 제거
				nFishPos[num][0] = NONE;
				nFishPos[num][1] = NONE;	//	잡아먹은 물고기 좌표 제거
				nFishDir[num] = NONE;		//	잡아먹은 물고기 방향 제거
				
				backtracking(nSharkY, nSharkX, nSharkDir, nEat, nMap, nFishPos, nFishDir);
				
				nMap[y][x] = num;		//	잡아먹은 물고기 복구
				nFishPos[num][0] = y;
				nFishPos[num][1] = x;	//	잡아먹은 물고기 좌표 복구
				nFishDir[num] = dir;	//	잡아먹은 물고기 방향 복구
			}	
		}
	}
	
	private static void moves(int sharkY, int sharkX, int[][] map, int[][] fishPos, int[] fishDir) {
		for(int num = 1; num <= SIZE * SIZE; num++) {	//	이동할 물고기 번호
			if(fishPos[num][0] == NONE || fishPos[num][1] == NONE || fishDir[num] == NONE)	//	이미 잡아먹힌 물고기일 경우
				continue;
			
			int cy = fishPos[num][0];
			int cx = fishPos[num][1];	//	num 물고기 좌표
			int cdir = fishDir[num];	//	현재 물고기 방향
			
			int yy = NONE;
			int xx = NONE;		//	num 물고기가 최종적으로 이동할 방향
			int dir = NONE;		//	num 물고기 최종 방향
			
			for(int d = 0; d < 8; d++) {
				int ndir = (cdir + d) % 8;
				int ny = cy + dy[ndir];
				int nx = cx + dx[ndir];	//	(ny, nx)로 이동 시도
				
				//	(ny, nx)가 범위 밖이거나, 상어가 있을 경우
				if(!isIn(ny, nx) || (ny == sharkY && nx == sharkX))
					continue;
				
				yy = ny;
				xx = nx;
				dir = ndir;
				break;
			}
			
			if(yy != NONE && xx != NONE && dir != NONE) {	//	이동할 수 있을 경우
				fishPos[num][0] = yy;
				fishPos[num][1] = xx;	//	num은 (yy, xx)로 이동
				fishDir[num] = dir;		//	num은 확정된 방향을 가짐
				
				if(map[yy][xx] == NONE)	//	해당 칸에 물고기가 없을 경우
					map[cy][cx] = NONE;	//	원래 num이 있던 자리는 NONE이 됨
				else {
					int nnum = map[yy][xx];	//	(yy, xx)에 있던 물고기 번호
					fishPos[nnum][0] = cy;
					fishPos[nnum][1] = cx;	//	nnum은 (cy, cx)로 이동
					map[cy][cx] = nnum;		//	원래 num이 있던 자리에는 nnum이 있음
				}
				
				map[yy][xx] = num;
			}
		}
	}
	
	//	1차원 배열 복사
	private static int[] copyArr(int[] orig) {
		int len = orig.length;
		int[] res = new int[len];
		
		for(int i = 0; i < len; i++)
			res[i] = orig[i];
		
		return res;
	}
	
	//	2차원 배열 복사
	private static int[][] copyArr(int[][] orig) {
		int row = orig.length;
		int col = orig[0].length;
		int[][] res = new int[row][col];
		
		for(int r = 0; r < row; r++) {
			for(int c = 0; c < col; c++)
				res[r][c] = orig[r][c];
		}
		
		return res;
	}
	
	private static final int[] dy = {-1, -1, 0, 1, 1, 1, 0, -1};
	private static final int[] dx = {0, -1, -1, -1, 0, 1, 1, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < SIZE) && (0 <= x && x < SIZE);
	}
}	//	Main-class-end
```

### 삼성전자 2022년 상반기 오후 2번 나무박멸

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/tree-kill-all/description?page=4&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int WALL = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	맵 크기
	private static int M;	//	시간
	private static int K;	//	제초제 뿌릴 범위
	private static int C;	//	제초제 지속 시간
	
	private static int[][] map;			//	나무, 벽 배치 상태
	private static int[][] killed;		//	killed[r][c] : (r, c)칸에 놓인 제초제가 만기되는 시간
	
	private static long ans = 0L;	//	M년 동안 박멸한 나무 수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		killed = new int[N][N];
		
		for(int r = 0; r < N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < N; c++)
				map[r][c] = Integer.parseInt(st.nextToken());
		}
		
		for(int year = 1; year <= M; year++) {
			step1();
			step2(year);
			step3(year);
		}
		
		System.out.println(ans);
	}	//	main-end
	
	//	1. 나무 성장
	private static void step1() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				if(map[r][c] > 0) {	//	(r, c)칸에 나무가 있을 경우
					int neighbors = 0;	//	이웃한 칸 중 나무가 있는 칸의 개수
					
					for(int d = 4; d < 8; d++) {
						int nr = r + dy[d];
						int nc = c + dx[d];
						
						if(isIn(nr, nc) && map[nr][nc] > 0)	//	주변 칸에 나무가 있을 경우
							neighbors++;
					}
				
					map[r][c] += neighbors;
				}
			}
		}
	}
	
	//	2. 나무 번식
	private static void step2(int year) {
		int[][] add = new int[N][N];	//	add[r][c] : (r, c)칸에 추가될 나무 수
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				if(map[r][c] > 0) {	//	(r, c)에 나무가 있을 경우
					int canCnt = 0;	//	(r, c) 주변 4칸 중 번식 가능한 칸의 개수
					
					int[] canDir = new int[4];
					
					for(int d = 4; d < 8; d++) {
						int nr = r + dy[d];
						int nc = c + dx[d];	//	(r, c) 주변 칸
						
						if(isIn(nr, nc) && map[nr][nc] == 0 && killed[nr][nc] < year)	//	(nr, nc)에 번식이 가능할 경우
							canDir[canCnt++] = d;	//	d 방향으로 번식 가능, 번식 가능한 칸의 개수 1 증가
					}
					
					for(int d = 0; d < canCnt; d++) {
						int dir = canDir[d];	//	dir 방향으로 나무 번식 가능
						
						add[r + dy[dir]][c + dx[dir]] += map[r][c] / canCnt;	//	해당 칸에 나무 번식
					}
				}
			}
		}
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++)
				map[r][c] += add[r][c];	//	나무 증가분 반영
		}
	}
	
	//	제초제 뿌리기
	private static void step3(int year) {
		int maxKill = -1;	//	가장 많은 나무를 죽일 수 있는 칸에 제초제 뿌렸을때, 그 때 죽인 나무 수
		int maxR = -1;
		int maxC = -1;		//	그때 제초제 뿌린 칸 수
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				int kill = 0;	//	(r, c)에 제초제 뿌렸을 때 죽이는 나무 수
				
				if(map[r][c] > 0) { 	//	나무가 하나라도 있을 경우
					kill = map[r][c];	//	해당 칸 나무 죽임
					
					for(int d = 0; d < 4; d++) {
						for(int k = 1; k <= K; k++) {
							int nr = r + dy[d] * k;
							int nc = c + dx[d] * k;	//	제초제 뿌릴 칸
							
							if(!isIn(nr, nc))	//	범위를 벗어날 경우, 해당 방향으로는 더 이상 뿌릴 수 없음
								break;
							
							//	다음 칸에 나무가 없거나, 벽일 경우, 제초제만 뿌리고 더 퍼지지 않음
							if(map[nr][nc] == 0 || map[nr][nc] == WALL)
								break;	//	해당 방향으로는 더 이상 퍼지지 않음
							
							kill += map[nr][nc];	//	(nr, nc)에 있는 나무 죽임
						}
					}
				}
				
				if(maxKill < kill) {
					maxKill = kill;
					maxR = r;
					maxC = c;
				}
			}
		}
		
		//	(maxR, maxC)를 중심으로 K 범위만큼 나무 죽이기
		if(map[maxR][maxC] > 0) {	//	해당 칸이 나무일 경우 
			ans += map[maxR][maxC];
			map[maxR][maxC] = 0;
		}
		killed[maxR][maxC] = year + C;
		
		for(int d = 0; d < 4; d++) {
			for(int k = 1; k <= K; k++) {
				int nr = maxR + dy[d] * k;
				int nc = maxC + dx[d] * k;	//	제초제 뿌릴 칸
				
				if(!isIn(nr, nc))	//	범위를 벗어날 경우, 해당 방향으로는 더 이상 뿌릴 수 없음
					break;
				
				killed[nr][nc] = year + C;	//	(nr, nc)에 뿌려진 제초제는 year + C까지 유효
				
				//	다음 칸에 나무가 없거나, 벽일 경우, 제초제만 뿌리고 더 퍼지지 않음
				if(map[nr][nc] == 0 || map[nr][nc] == WALL)
					break;	//	해당 방향으로는 더 이상 퍼지지 않음
				
				ans += map[nr][nc];	//	(nr, nc)에 있는 나무 죽임
				map[nr][nc] = 0;
			}
		}
	}
	
	private static final int[] dy = {-1, 1, 1, -1, 0, 1, 0, -1};
	private static final int[] dx = {1, 1, -1, -1, 1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```