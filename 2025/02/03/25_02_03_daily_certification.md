# 25_02_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23288 주사위 굴리기 2

[23288번: 주사위 굴리기 2](http://boj.ma/23288/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int[] dice = {0, 1, 2, 3, 4, 5, 6};
	
	private static int N;
	private static int M;
	private static int K;
	
	private static int[][] board;
	
	private static int diceY = 0;
	private static int diceX = 0;		//	주사위 위치 (시작 시 (0, 0))
	private static int diceDir = 0;		//	주사위 이동방향 (시작 시 동쪽 (0))
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		board = new int[N][M];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++)
				board[y][x] = Integer.parseInt(st.nextToken());
		}
		
		for(int k = 0; k < K; k++) {
			step1();
			step2();
			step3();
		}
		
		System.out.println(answer);
	}	//	main-end
	
	private static void step1() {
		int ny = diceY + dy[diceDir];
		int nx = diceX + dx[diceDir];	//	현재 이동방향으로 이동했을때 다음 칸
		
		if(!isIn(ny, nx)) {	//	다음 칸이 범위 밖일 경우
			diceDir = diceDir == 0 ? 2 : (diceDir == 2 ? 0 : (diceDir == 1 ? 3 : 1));	//	역방향으로 바꿈
			ny = diceY + dy[diceDir];
			nx = diceX + dx[diceDir];
		}
		
		roll(diceDir);	//	해당 방향으로 굴러감
		diceY = ny;
		diceX = nx;		//	주사위 위치 조정
	}
	
	private static void step2() {
		Queue<int[]> queue = new LinkedList<>();
		boolean[][] visited = new boolean[N][M];
		int bScore = board[diceY][diceX];
		int cScore = 1;
		
		visited[diceY][diceX] = true;
		queue.offer(new int[] {diceY, diceX});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && board[ny][nx] == bScore && !visited[ny][nx]) {
					cScore++;
					visited[ny][nx] = true;
					queue.offer(new int[] {ny, nx});
				}
			}
		}
		
		answer += bScore * cScore;
	}
	
	private static void step3() {
		int aScore = dice[6];				//	주사위 아랫면 점수
		int bScore = board[diceY][diceX];	//	주사위가 있는 칸의 점수
		
		if(aScore > bScore)
			diceDir = (diceDir + 1) % 4;
		else if(aScore < bScore)
			diceDir = (diceDir + 3) % 4;
		
	}
	
	private static void roll(int dir) {
		int[] tmp = new int[7];
		
		if(dir == 0 || dir == 2) {
			tmp[2] = dice[2];
			tmp[5] = dice[5];
			
			if(dir == 0) {	//	동쪽으로 구를 경우
				tmp[1] = dice[4];
				tmp[3] = dice[1];
				tmp[4] = dice[6];
				tmp[6] = dice[3];
			}
			else {			//	서쪽으로 구를 경우
				tmp[1] = dice[3];
				tmp[3] = dice[6];
				tmp[4] = dice[1];
				tmp[6] = dice[4];
			}
		}
		else {
			tmp[3] = dice[3];
			tmp[4] = dice[4];
			
			if(dir == 1) {	//	남쪽으로 구를 경우
				tmp[1] = dice[2];
				tmp[2] = dice[6];
				tmp[5] = dice[1];
				tmp[6] = dice[5];
			}
			else {			//	북쪽으로 구를 경우
				tmp[1] = dice[5];
				tmp[2] = dice[1];
				tmp[5] = dice[6];
				tmp[6] = dice[2];
			}
		}
		
		dice = tmp;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```

### **BOJ** 2696 중앙값 구하기

[2696번: 중앙값 구하기](http://boj.ma/2696/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.PriorityQueue;
import java.util.Collections;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int T;
	private static int N;

	// maxPq의 원소 개수가 minPq의 원소 개수와 같거나 1개 더 많도록 유지
	// maxPq의 최댓값이 minPq의 최솟값보다 작거나 같도록 유지
	// 전체 원소 개수가 홀수개일때 maxPq의 최댓값이 중앙값임
	private static final PriorityQueue<Integer> maxPq = new PriorityQueue<>(Collections.reverseOrder());
	private static final PriorityQueue<Integer> minPq = new PriorityQueue<>();

	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());

		for (int tc = 1; tc <= T; tc++) {
			StringBuilder lb = new StringBuilder();
			int midCnt = 0; // 출력하는 중앙값 개수
			int lineCnt = 0; // 한 줄에 출력하는 개수

			N = Integer.parseInt(br.readLine());

			maxPq.clear();
			minPq.clear();

			int order = 1;
			for (int row = 1; row <= N / 10 + (N % 10 != 0 ? 1 : 0); row++) {
				st = new StringTokenizer(br.readLine());

				while (st.hasMoreTokens()) {
					int num = Integer.parseInt(st.nextToken());

					if (order % 2 == 1) { // 홀수번째일경우 maxPq에 넣어 항상 maxPq의 원소 개수가 minPq 원소개수와 같거나 1개 더 많도록 유지
						if (!minPq.isEmpty() && num > minPq.peek()) { // maxPq에 추가될 값이 minPq의 최솟값보다 클 경우
							int out = minPq.poll();
							minPq.offer(num);
							maxPq.offer(out); // num을 minPq에 넣고, 기존 minPq의 최솟값을 maxPq에 넣음
						} else
							maxPq.offer(num);

						midCnt++;
						lb.append(maxPq.peek()).append(" ");
						lineCnt++;
						if (lineCnt == 10) { // 한 줄에 10개를 출력했으면
							lineCnt = 0;
							lb.append("\n"); // 줄 바꾸기
						}
					} else { // 짝수번째일 경우 minPq에 넣기
						if (num < maxPq.peek()) { // minPq에 추가될 값이 maxPq의 최댓값보다 작을 경우
							int out = maxPq.poll();
							maxPq.offer(num);
							minPq.offer(out); // num을 maxPq에 넣고, 기존 maxPq의 최댓값을 minPq에 넣기
						} else
							minPq.offer(num);
					}
					
					order++;
				}
			}

			sb.append(midCnt).append("\n");
			sb.append(lb.toString());
			if (midCnt % 10 != 0)
				sb.append("\n");
		}

		System.out.print(sb);
	} // main-end
} // Main-class-end
```