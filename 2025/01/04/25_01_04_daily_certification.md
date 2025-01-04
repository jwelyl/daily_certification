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

### **BOJ** 14890 경사로

[14890번: 경사로](https://boj.ma/14890/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int L;
	private static int[][] map;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		L = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				map[y][x] = Integer.parseInt(st.nextToken());
		}

		for(int y = 0; y < N; y++) 
			checkRow(y);
		for(int x = 0; x < N; x++)
			checkCol(x);
		
		System.out.println(answer);
	}	//	main-end
	
	private static boolean check(List<Chunk> chunks) {
		for(int i = 0; i < chunks.size() - 1; i++) {
			Chunk cur = chunks.get(i);
			Chunk next = chunks.get(i + 1);
			
			if(cur.height > next.height) {	//	next 쪽에 경사로를 놓아야 할 경우
				if(next.length < L)	//	경사로를 놓을 칸이 부족할 경우
					return false;
				next.decrease();
			}
			else {	//	cur 쪽에 경사로를 놓아야 할 경우
				if(cur.length < L)	//	경사로를 놓을 칸이 부족할 경우
					return false;
				cur.decrease();
			}
		}
		
		return true;
	}
	
	private static void checkRow(int row) {
		List<Chunk> chunks = new ArrayList<>();
		
		Chunk cur = new Chunk(map[row][0]);
		
		for(int x = 1; x < N; x++) {
			if(map[row][x] == cur.height)	//	현재 chunk와 높이가 같을 경우
				cur.increase();	//	현재 chunk 길이 1 증가
			else {	//	현재 chunk와 높이가 다를 경우
				if(Math.abs(map[row][x] - cur.height) > 1)	//	인접한 두 칸의 높이 차이가 1보다 클 경우, 경사로를 놓을 수 없음
					return;
				
				chunks.add(cur);	//	현재 chunk 끝, 저장
				cur = new Chunk(map[row][x]);	//	새로운 chunk 생성
			}
		}
		chunks.add(cur);	//	마지막 chunk 저장
		
		if(check(chunks))	//	경사로를 놓을 수 있을 경우
			answer++;
	}
	
	private static void checkCol(int col) {
		List<Chunk> chunks = new ArrayList<>();
		
		Chunk cur = new Chunk(map[0][col]);
		
		for(int y = 1; y < N; y++) {
			if(map[y][col] == cur.height)	//	현재 chunk와 높이가 같을 경우
				cur.increase();	//	현재 chunk 길이 1 증가
			else {	//	현재 chunk와 높이가 다를 경우
				if(Math.abs(map[y][col] - cur.height) > 1)	//	인접한 두 칸의 높이 차이가 1보다 클 경우, 경사로를 놓을 수 없음
					return;
				
				chunks.add(cur);	//	현재 chunk 끝, 저장
				cur = new Chunk(map[y][col]);	//	새로운 chunk 생성
			}
		}
		chunks.add(cur);	//	마지막 chunk 저장

		if(check(chunks))
			answer++;
	}
	
	//	높이가 같은 연속한 칸들을 Chunking한 결과 
	private static class Chunk {
		public int height;		//	높이가 같은 연속한 칸들의 높이
		public int length;		//	높이가 같은 칸들의 개수, Chunk 길이
		
		public Chunk(int height) {
			this.height = height;
			this.length = 1;
		}
		
		public void increase() {	//	높이가 같은 칸 추가 시 Chunk 길이 1 증가
			this.length++;
		}
		
		public void decrease() {
			this.length -= L;
		}
	}
}	//	Main-class-end
```

### **BOJ 23291** 어항 정리

[23291번: 어항 정리](https://boj.ma/23291/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;	//	어항이 아님
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int K;
	
	private static int[][] bowls;
	
	private static int answer = 0;	//	어항 정리 횟수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		bowls = new int[N][N];
		
		st = new StringTokenizer(br.readLine());
		for(int x = 0; x < N; x++)
			bowls[N - 1][x] = Integer.parseInt(st.nextToken());
		
		while(true) {
			if(step1()) {	//	더 이상 정리할 필요가 없을 경우
				System.out.println(answer);
				break;
			}
			
			answer++;	//	정리가 필요할 경우, 정리 횟수 1 증가
			step2();
			step3();
		}
	}	//	main-end
	
	private static boolean step1() {
		int min = Integer.MAX_VALUE;	//	물고기가 가장 적은 어항의 물고기 수
		int max = Integer.MIN_VALUE;	//	물고기가 가장 많은 어항의 물고기 수
		List<Integer> minPos = new ArrayList<>();
		
		for(int x = 0; x < N; x++) {
			max = Math.max(max, bowls[N - 1][x]);
			
			if(bowls[N - 1][x] < min) {
				min = bowls[N - 1][x];
				minPos.clear();
				minPos.add(x);
			}
			else if(bowls[N - 1][x] == min)
				minPos.add(x);
		}
		
		for(int x : minPos)	//	물고기가 가장 적은 칸의 물고기 수 1 증가
			bowls[N - 1][x]++;
		
		return max - min <= K;	//	최댓값 - 최솟값 <= K일 경우 정리할 필요 없음
	}
	
	private static void step2() {
		int row = 1;
		int col = 1;	//	bowls에서 떼낼 부분 배열 크기
		int ey = N - 1;
		int sx = 0;			//	떼낼 부분 배열의 좌하단 좌표 (ey, sx) 
		
		for(int rot = 0; ; rot++) {
			int sy = ey - row + 1;
			int ex = sx + col - 1;	//	떼낼 배열 (sy, sx) - (ey, ex)
			
			if(ex + row  >= N)	//	배열을 떼내서 회전시킨 다음에 올렸을 때, 범위를 벗어날 경우, 즉 떼낼 수 없을 경우
				break;
			
			int[][] detached = detach(sy, sx, ey, ex);	//	떼낸 배열
			int[][] rotated = rotate90(detached);		//	떼낸 배열을 시계방향으로 90도 회전시킨 배열
			paste(rotated, N - 2, ex + 1);					//	회전시킨 배열을 (1, ex + 1)에 붙임
			
			if(rot % 2 == 0)	//	다음에 떼어낼 배열 크기 결정
				row++;
			else
				col++;
			
			sx = ex + 1;
		}
		
		adjust();
		flatten();
	}
	
	private static void step3() {
		int[][] detached = detach(N - 1, 0, N - 1, N / 2 - 1);	//	일렬로 놓인 N개 어항 중 왼쪽 절반을 떼어냄
		int[][] rotated = rotate180(detached);					//	떼어낸 어항을 180도 회전시킴
		paste(rotated, N - 2, N / 2);	//	회전시킨 어항을 다시 붙임
		detached = detach(N - 2, N / 2, N - 1, 3 * N / 4 - 1);
		rotated = rotate180(detached);
		paste(rotated, N - 3, 3 * N / 4);
		
		adjust();
		flatten();
	}
	
	//	모든 인접한 두 어항 사이의 물고기 수 조절
	private static void adjust() {
		int[][] diffs = new int[N][N];	//	diff[y][x] : (y, x) 칸의 증감량
		
		for(int y = N - 1; y >= 0; y--) {
			for(int x = N - 1; x >= 0; x--) {
				if(bowls[y][x] == NONE)	//	(y, x)가 어항이 아닐 경우
					continue;
				
				for(int dir = 0; dir < 2; dir++) {
					int ny = y + dy[dir];
					int nx = x + dx[dir];
					
					if(isIn(ny, nx) && bowls[ny][nx] != NONE) {
						int d = Math.abs(bowls[ny][nx] - bowls[y][x]) / 5;
						
						if(d != 0) {
							if(bowls[ny][nx] > bowls[y][x]) {
								diffs[ny][nx] -= d;
								diffs[y][x] += d;
							}
							else {
								diffs[ny][nx] += d;
								diffs[y][x] -= d;
							}
						}
					}
				}
			}
		}
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				bowls[y][x] += diffs[y][x];
		}
	}
	
	//	어항을 다시 일렬로 평평하게 배치
	private static void flatten() {
		int[][] tmp = new int[N][N];
		
		int xpos = 0;
		
		for(int x = 0; x < N; x++) {
			int y = N - 1;

			while(y >= 0 && bowls[y][x] != NONE) {
				tmp[N - 1][xpos++] = bowls[y][x];
				y--;
			}
		}
		
		bowls = tmp;
	}
	
	//	bowls에서 좌상단이 (sy, sx), 우하단이 (ey, ex)인 부분 배열을 떼서 배열로 만듬
	//	bowls에서 떼낸 부분은 0으로 변경
	private static int[][] detach(int sy, int sx, int ey, int ex) {
		int row = ey - sy + 1;
		int col = ex - sx + 1;
		int[][] res = new int[row][col];
		
		for(int y = sy; y <= ey; y++) {
			for(int x = sx; x <= ex; x++) {
				res[y - sy][x - sx] = bowls[y][x];
				bowls[y][x] = NONE;	//	(y, x)를 떼냈으므로 (y, x)는 어항이 아님
			}
		}
		
		return res;
	}
	
	//	회전된 배열을 다시 bowls의 (ey, sx)를 좌하단으로 해서 붙임
	private static void paste(int[][] rotated, int ey, int sx) {
		int row = rotated.length;
		int col = rotated[0].length;
		
		int sy = ey - row + 1;
		int ex = sx + col - 1;
		
		for(int y = 0; y < row; y++) {
			for(int x = 0; x < col; x++)
				bowls[sy + y][sx + x] = rotated[y][x];
		}
	}
	
	//	detached 배열을 시계방향으로 90도 회전시킨 배열 반환
	private static int[][] rotate90(int[][] detached) {
		int row = detached.length;
		int col = detached[0].length;
		int[][] res = new int[col][row];
		
		for(int y = 0; y < row; y++) {
			for(int x = 0; x < col; x++)
				res[x][row - y - 1] = detached[y][x];
		}
		
		return res;
	}
	
	//	detached 배열을 180도 회전시킨 배열 반환
	private static int[][] rotate180(int[][] detached) {
		int row = detached.length;
		int col = detached[0].length;
		int[][] res = new int[row][col];
		
		for(int y = 0; y < row; y++) {
			for(int x = 0; x < col; x++) {
				int yy = row - y - 1;
				int xx = col - x - 1;
				
				res[yy][xx] = detached[y][x];
			}
		}
		
		return res;
	}
	
	private static final int[] dy = {0, -1};
	private static final int[] dx = {-1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```