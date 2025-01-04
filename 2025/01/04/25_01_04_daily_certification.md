# 25_01_04_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 22862 가장 긴 짝수 연속한 부분 수열 (large)

[22862번: 가장 긴 짝수 연속한 부분 수열 (large)](https://boj.ma/22862/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int K;
	private static int[] nums;
	
	private static int start = 0;
	private static int end = 0;
	private static int odds = 0;		//	[start, end] 구간에 포함된 홀수 개수
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());

		nums = new int[N];
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			nums[i] = Integer.parseInt(st.nextToken());
		
		while(end < N) {
			if(nums[end] % 2 == 0) {	//	이번에 추가될 수가 짝수일 경우
				answer = Math.max(answer, end - start + 1 - odds);	//	짝수 개수 = [start, end] 구간 길이 - [start, end] 구간에 포함된 홀수 개수
				end++;	//	다음 수 확인하러 가기
			}
			else {	//	이번에 추가될 수가 홀수일 경우
				if(odds + 1 <= K) {	//	[start, end] 구간에 아직 홀수를 더 담을 수 있을 경우
					odds++;		//	end에 있는 홀수 담기
					end++;		//	다음 수 확인하러 가기
				}
				else {	//	이미 [start, end]에 홀수가 K개 존재할 경우
					if(nums[start] % 2 == 1)	//	start의 수가 홀수일 경우
						odds--;	//	홀수 개수 1개 감소
					
					start++;
				}
			}
		}
		
		System.out.println(answer);
	}	//	main-end
}	//	Main-class-end
```

### **BOJ 21609** 상어 중학교

[21609번: 상어 중학교](https://boj.ma/21609/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int BLACK = -1;					//	검은색 블록
	private static final int RAINBOW = 0;					//	무지개 색 블록
	private static final int NONE = Integer.MAX_VALUE;		//	블록 없음
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	블록 그룹 수
	private static int M;	//	일반 블록 색 (1 ~ M)
	
	private static int[][] board;			//	격자
	private static boolean[][] visited;		//	여러번 bfs 시 중복 bfs 방지 위한 전역 방문 처리 배열
	private static int[] selected;			//	선택받은 블록 그룹
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		board = new int[N][N];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				board[y][x] = Integer.parseInt(st.nextToken());
		}
		
		while(true) {
			visited = new boolean[N][N];
			selected = null;	//	선택받은 블록 그룹
			
			step1();	//	가장 우선순위가 높은 블록 그룹 선택
			
			if(selected == null) {	//	블록 그룹이 더 이상 없을 경우
				System.out.println(answer);
				break;
			}
			
			step2();	//	step1에서 선택받은 블록 그룹 제거
			step3();					//	중력 작용 후 반시계방향 90도 회전 후 다시 중력 작용
		}
	}	//	main-end
	
	private static void step1() {
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++) {
				if(1 <= board[y][x] && board[y][x] <= M && !visited[y][x]) {	//	(y, x)칸이 일반 블록이고 아직 방문하지 않았을 경우
					int[] group = bfs(y, x);
					
					if(group != null)	// 블록 그룹이 존재할 경우
						compare(group);
				}
			}
		}
	}
	
	//	(y, x)에 있는 일반 블록을 기준 블록으로 하는 블록 그룹 반환
	//	존재할 경우 해당 블록 그룹 반환, 존재하지 않을 경우, null 반환
	private static int[] bfs(int y, int x) {
		int[] res = {1, 0, y, x};	//	{일반 블록 수, 무지개블록 수, 기준 블록 y 좌표, 기준 블록 x 좌표}
		boolean[][] checked = new boolean[N][N];	//	bfs 함수 내에서 중복 방문 방지 위한 방문 배열
		
		Queue<int[]> queue = new LinkedList<>();
		visited[y][x] = true;
		checked[y][x] = true;
		queue.offer(new int[] {y, x});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	인접한 칸
				
				//	인접한 칸이 범위를 벗어날 경우
				if(!isIn(ny, nx))
					continue;
				
				//	같은 색의 일반 블록이거나 무지개색 블록일 경우
				if(board[ny][nx] == board[y][x] || board[ny][nx] == RAINBOW) {
					if(!checked[ny][nx]) {	//	해당 bfs에서 아직 방문 안했을 경우
						checked[ny][nx] = true;	//	해당 bfs에서 다시 방문하지 않도록
						visited[ny][nx] = true;	//	다음 bfs에서 (ny, nx)에서 시작하는 일 없도록
						if(board[ny][nx] == board[y][x])	//	일반 블록일 경우
							res[0]++;
						else								//	무지개색 블록일 경우
							res[1]++;
						
						queue.offer(new int[] {ny, nx});
					}
				}
			}
		}
		
		if(res[0] + res[1] < 2)	//	전체 블록 개수가 2개보다 적을 경우
			return null;
		
		return res;
	}
	
	//	선택받은 블록 그룹을 제거
	private static void step2() {
		int y = selected[2];
		int x = selected[3];		//	제거할 블록 그룹의 기준 블록 좌표
		int check = board[y][x];	//	해당 블록 그룹의 표식
		
		delete(y, x, check);
		answer += (selected[0] + selected[1]) * (selected[0] + selected[1]);	//	블록 그룹 크기^2만큼 점수 추가
	}
	
	//	(y, x)를 시작으로 도달 가능한 같은 색 일반 블록, 무지개 블록 제거
	private static void delete(int y, int x, int check) {
		Queue<int[]> queue = new LinkedList<>();
		boolean[][] checked = new boolean[N][N];	//	delete 함수 내에서 중복 방문 방지 위한 방문 배열
		board[y][x] = NONE;
		checked[y][x] = true;
		queue.offer(new int[] {y, x});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	인접한 칸
				
				if(isIn(ny, nx) && (board[ny][nx] == RAINBOW || board[ny][nx] == check) && !checked[ny][nx]) {	//	범위를 벗어나지 않고 같은 그룹의 블록일 경우
					checked[ny][nx] = false;
					board[ny][nx] = NONE;
					queue.offer(new int[] {ny, nx});
				}
			}
		}
	}
	
	private static void step3() {
		gravity();	//	중력 작용
		turn90();	//	반시계방향 90도 회전
		gravity();	//	중력 작용
	}
	
	private static void gravity() {
		for(int x = 0; x < N; x++) {	//	모든 열에 대해서 진행
			for(int ey = 0; ey <= N - 2; ey++) {	//	떨어질 가장 위에 있는 블록의 y 좌표
				for(int y = N - 2; y >= ey; y--) {
					if(board[y][x] == NONE || board[y][x] == BLACK || board[y + 1][x] != NONE)	//	해당 칸이 빈칸이거나 검은 블록일경우, 또는 아랫칸에 이미 블록이 존재할 경우
						continue;
					
					board[y + 1][x] = board[y][x];	//	(y, x)에 있던 블록이 한칸 내려감
					board[y][x] = NONE;				//	(y, x)는 빈칸으로 변경
				}
			}
		}
	}
	
	//	selected와 group을 비교
	private static void compare(int[] group) {
		if(selected == null) {
			selected = group;
			return;
		}
		
		int selectedSize = selected[0] + selected[1];
		int groupSize = group[0] + group[1];
		
		if(selectedSize < groupSize) {	//	크기가 더 클 경우
			selected = group;	//	대체
			return;
		}
		
		if(selectedSize > groupSize)	//	크기가 더 작을 경우 탈락
			return;
		
		//	둘의 크기가 같을 경우
		int selectedRainbows = selected[1];
		int groupRainbows = group[1];
		
		if(selectedRainbows < groupRainbows) {	//	무지개블록이 더 많을 경우
			selected = group;	//	대체
			return;
		}
		
		if(selectedRainbows > groupRainbows)	//	크기가 더 작을 경우 탈락
			return;
		
		int sy = selected[2];
		int sx = selected[3];
		int gy = group[2];
		int gx = group[3];
		
		if(sy < gy) {
			selected = group;	//	대체
			return;
		}
		
		if(sy > gy)	//	기준 블록 y 좌표가 더 작을 경우
			return;
		
		if(sx < gx)	//	대체
			selected = group;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
	
	//	board를 반시계 방향으로 90도로 돌림
	private static void turn90() {
		int[][] tmp = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				tmp[N - x - 1][y] = board[y][x];
		}
		
		board = tmp;
	}
}	//	Main-class-end
```