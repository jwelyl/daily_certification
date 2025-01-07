# 25_01_07_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 21611** 마법사 상어와 블리자드

[21611번: 마법사 상어와 블리자드](https://boj.ma/21611/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;	//	구슬이 없음
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;			//	맵 크기 N * N
	private static int M;			//	블리자드 시전 횟수
	private static int[][] map;		//	맵
	
	private static int[][] numToPos;		//	numToPos[num] : 맵에서 num(0 <= num < N * N)번째 격자의 {y좌표, x좌표}
	private static int[][] posToNum;		//	posToNum[y][x] : 맵에서 (y, x) 격자의 순서 (0 <= num && num < N * N)
	private static int[] beeds;				//	beeds[num] : num번째 격자에 있는 구슬
	
	private static final int[] exploded = {0, 0, 0, 0};	//	exploded[i] : 폭발한 i번 구슬의 개수
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		init();
		map = new int[N][N];
		beeds = new int[N * N];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				map[y][x] = Integer.parseInt(st.nextToken());
				int num = posToNum[y][x];		//	(y, x) 격자의 격자 번호
				beeds[num] = map[y][x];			//	num번째 격자에 있는 구슬
			}
		}
		
		for(int m = 0; m < M; m++) {
			destroy();
			move();
			explode();
			transform();
		}
		
		for(int i = 1; i <= 3; i++)
			answer += i * exploded[i];
		
		System.out.println(answer);
	}	//	main-end
	
	//	dir 방향으로 거리가 size 이하인 모든 칸에 있는 구슬 파괴 
	private static void destroy() throws IOException {
		st = new StringTokenizer(br.readLine());
		int dir = Integer.parseInt(st.nextToken()) - 1;
		int size = Integer.parseInt(st.nextToken());
		
		int cy = N / 2;
		int cx = N / 2;
		
		for(int i = 1; i <= size; i++) {
			cy = cy + dy[dir];
			cx = cx + dx[dir];
			
			int num = posToNum[cy][cx];		//	(cy, cx) 격자의 격자 번호
			map[cy][cx] = NONE;				//	(cy, cx) 칸에 구슬이 사라짐
			beeds[num] = NONE;				//	해당 격자에는 구슬이 사라짐
		}
	}
	
	private static void explode() {
		while(true) {
			List<int[]> chunks = new ArrayList<>();	//	연속한 4개 이상 구슬의 격자 번호 [start, end]
			
			for(int start = 1; start < N * N;) {
				if(beeds[start] == NONE) {
					start++;
					continue;
				}
				
				int end = start + 1;
				
				while(end < N * N && beeds[end] == beeds[start])
					end++;
				
				if(end == N * N) {	//	end가 끝에 도달한 경우, [start, end - 1]까지 연속
					if(end - start >= 4)
						chunks.add(new int[] {start, end - 1});
					break;
				}
				else if(beeds[end] != beeds[start]) {	//	연속한 구슬이 끊긴 경우, [start, end - 1]까지 연속
					if(end - start >= 4)
						chunks.add(new int[] {start, end - 1});
					start = end;
				}
			}
			
			if(chunks.isEmpty())	//	연속되는 4개 구슬이 없을 경우, 폭발이 일어나지 않음
				break;
			
			for(int[] chunk : chunks) {
				int start = chunk[0];
				int end = chunk[1];
				int bnum = beeds[start];
				
				for(int num = start; num <= end; num++) {
					int y = numToPos[num][0];
					int x = numToPos[num][1];
					beeds[num] = NONE;
					map[y][x] = NONE;
				}
				
				exploded[bnum] += (end - start + 1);
			}
			
			move();
		}
	}
	
	//	구슬 변화 단계
	private static void transform() {
		List<int[]> groups = new ArrayList<>();	//	연속한 구슬 그룹
		
		for(int start = 1; start < N * N;) {
			if(beeds[start] == NONE) {
				start++;
				continue;
			}
			
			int end = start + 1;
			
			while(end < N * N && beeds[end] == beeds[start])
				end++;
			
			if(end == N * N) {	//	end가 끝에 도달한 경우, [start, end - 1]까지 연속
				groups.add(new int[] {beeds[start], start, end - 1});
				break;
			}
			else if(beeds[end] != beeds[start]) {	//	연속한 구슬이 끊긴 경우, [start, end - 1]까지 연속
				groups.add(new int[] {beeds[start], start, end - 1});
				start = end;
			}
		}
		
		redraw(groups);
	}
	
	private static void redraw(List<int[]> groups) {
		for(int num = 0; num < N * N; num++) {
			int y = numToPos[num][0];
			int x = numToPos[num][1];
			
			beeds[num] = NONE;
			map[y][x] = NONE;
		}
		
		int num = 1;
		
		for(int[] group : groups) {
			int start = group[1];
			int end = group[2];			//	[start, end]가 연속
			int a = end - start + 1;	//	연속한 구슬 개수
			int b = group[0];			//	연속한 구슬 종류
			
			if(num == N * N)	//	더 이상 넣을 수 없을 경우
				break;
			
			int y = numToPos[num][0];
			int x = numToPos[num][1];
			
			beeds[num] = a;
			map[y][x] = a;
			
			num++;
			
			if(num == N * N)	//	더 이상 넣을 수 없을 경우
				break;
			
			y = numToPos[num][0];
			x = numToPos[num][1];
			
			beeds[num] = b;
			map[y][x] = b;
			num++;
		}
	}
	
	//	빈칸이 생겨서 구슬이 이동
	private static void move() {
		int[] tmp = new int[N * N];
		
		int toNum = 1;
		for(int fromNum = 1; fromNum < N * N; fromNum++) {
			int fy = numToPos[fromNum][0];
			int fx = numToPos[fromNum][1];
			int ty = numToPos[toNum][0];
			int tx = numToPos[toNum][1];
			
			if(beeds[fromNum] != NONE) {
				tmp[toNum] = beeds[fromNum];
				map[fy][fx] = NONE;
				map[ty][tx] = beeds[fromNum];
				toNum++;
			}
		}
		
		beeds = tmp;
	}
	
	private static void init() {
		int[] dy = {0, 1, 0, -1};
		int[] dx = {1, 0, -1, 0};
		
		int dir = 0;
		int num = N * N - 1;
		int cy = 0;
		int cx = 0;
		
		numToPos = new int[N * N][2];
		posToNum = new int[N][N];
		
		while(num > 0) {
			posToNum[cy][cx] = num;
			numToPos[num][0] = cy;
			numToPos[num--][1] = cx;
			
			while(true) {
				int ny = cy + dy[dir];
				int nx = cx + dx[dir];

				if(isIn(ny, nx) && posToNum[ny][nx] == 0) {
					cy = ny;
					cx = nx;
					break;
				}
				
				dir = (dir + 1) % 4;
			}
		}
		
		numToPos[0][0] = N / 2;
		numToPos[0][1] = N / 2;
	}
	
	private static final int[] dy = {-1, 1, 0, 0};
	private static final int[] dx = {0, 0, -1, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```