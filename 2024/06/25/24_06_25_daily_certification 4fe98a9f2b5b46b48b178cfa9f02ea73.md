# 24_06_25_daily_certification

# Computer Architecture

# Overview

## 1. Data, Instruction

### 1-1. 데이터 (Data)

- **숫자, 문자, 이미지, 동영상과 같은 정적인 정보**
- 0, 1로 이루이짐
- 컴퓨터와 주고 받는 정보, 컴퓨터에 저장된 정보

### 1-2. 명령어 (Instruction)

- **컴퓨터를 실질적으로 움직이는 정보**
- 데이터는 명령어를 위해 존재하는 재료
- 컴퓨터 프로그램은 명령어의 집합

## 2. 컴퓨터의 4가지 핵심 부품

컴퓨터의 4가지 핵심 부품은 **CPU, Main Memory, Secondary Storage, I/O Device**이다.

![main_board.jpeg](24_06_25_daily_certification%204fe98a9f2b5b46b48b178cfa9f02ea73/main_board.jpeg)

### 2-1. 주기억 장치(Main Memory)

**현재 실행 중인 프로그램(프로세스)의 데이터와 명령어가 저장된 장치**

비싸고 저장 용량이 적으며, 전원이 꺼지면 메모리에 저장된 데이터, 명령어는 모두 사라진다. (휘발성 저장장치)

![main_memory.jpeg](24_06_25_daily_certification%204fe98a9f2b5b46b48b178cfa9f02ea73/main_memory.jpeg)

- 메모리에는 현재 실행 중인 프로그램(프로세스)의 데이터와 명령어가 저장되어 있다.
- 프로그램이 실행되기 위해서는 프로그램이 메모리에 저장되어야 한다.
    - 보조 기억 장치에 저장된 프로그램이 메모리에 올라오며 실행된다.
- 메모리에 저장된 명령어와 데이터는 **주소(Address)**를 통해 빠르게 접근할 수 있다.

### 2-2. 중앙 처리 장치(CPU)

**메모리에 저장된 명령어를 읽어들이고 해석하고 실행하는 장치**

크게 ALU, Register, Control Unit으로 구성됨

![cpu.jpeg](24_06_25_daily_certification%204fe98a9f2b5b46b48b178cfa9f02ea73/cpu.jpeg)

- **ALU** : 프로그램 내부에서 수행되는 계산을 수행하는 부품
- **Register** : CPU 내부의 작은 임시 저장 장치로 프로그램 실행 시 필요한 값들을 저장함, 레지스터마다 역할이 다름
- **Control Unit** : 메모리와 같은 컴퓨터 부품을 제어하는 전기 신호를 보내고, 명령어를 해석하는 부품 ****

### 2-3. 보조 기억 장치(Secondary Storage)

**메모리와 다르게 컴퓨터 전원이 꺼져도 데이터를 영구히 저장하는 장치 (비휘발성 저장 장치)**

![secondary_storage.jpeg](24_06_25_daily_certification%204fe98a9f2b5b46b48b178cfa9f02ea73/secondary_storage.jpeg)

- 메모리보다 저렴하고 저장 용량이 크다.
- 메모리가 실행 중인 프로세스를 저장한다면, 보조 기억 장치는 프로그램 파일을 영구히 저장한다.
- HDD, Disk, USB Memory 등이 존재한다.

### 2-4. 입출력 장치(I/O Device)

**모니터, 키보드, 마우스와 같이 컴퓨터 외부에 연결되어 컴퓨터 내부와 정보를 주고 받는 장치**

넓은 의미에서 Secondary Storage도 I/O Device에 포함 (Peripheral Device)

보통은  Secondary Storage는 Main Memory를 보조하는 특수한 I/O Device로 구분함

![io_device.jpeg](24_06_25_daily_certification%204fe98a9f2b5b46b48b178cfa9f02ea73/io_device.jpeg)

### 2-5. Main Board, System Bus

**컴퓨터의 4가지 핵심 부품은 Main Board라는 판에 연결되어 있다.** 

Main Board에 연결된 부품들은 **Bus**라는 통로를 통해 서로 연결되어 있다.

![main_board.jpeg](24_06_25_daily_certification%204fe98a9f2b5b46b48b178cfa9f02ea73/main_board%201.jpeg)

**System Bus**

CPU, Main Memory, Secondary Storage, I/O Device를 연결하는 핵심적인 Bus

- Control Bus : 메모리 읽기, 쓰기와 같은 제어 신호를 주고 받는 통로
- Address Bus : 명령어, 데이터를 읽고 쓰기 위해 메모리와 주소를 주고 받는 통로
- Data Bus : 명령어, 데이터를 주고 받는 통로

# Problem Solving (Algorithm & SQL)

### BOJ 14391 종이 조각

[](https://www.acmicpc.net/problem/14391)

**Bruteforcing + Bitmasking**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int N;
	private static int M;
	private static char[][] board;
	private static char[][] bits;
	
	private static int ans = -1;
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		N = Integer.parseInt(tokens.nextToken());
		M = Integer.parseInt(tokens.nextToken());
		
		board = new char[N][M];
		bits = new char[N][M];
		
		for(int r = 0; r < N; r++)
			board[r] = br.readLine().toCharArray();
		
		for(int i = 0; i < Math.pow(2, N * M); i++) {
			String bit = String.format("%16s", Integer.toBinaryString(i)).replace(" ", "0").substring(16 - N * M);
			
			for(int r = 0; r < N; r++)
				bits[r] = bit.substring(r * M, r * M + M).toCharArray();
			
			ans = Math.max(ans, valueOf(bits));
		}
		
		System.out.println(ans);
	}	//	main-end

	private static int valueOf(char[][] bits) {
		int sum = 0;
		boolean[][] visited = new boolean[N][M];
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < M; c++) {
				if(visited[r][c])	//	이미 이전 종이 조각에 포함된 칸일 경우
					continue;
				
				visited[r][c] = true;
				
				int partSum = (board[r][c] - '0');			//	(r, c)에서 시작하는 종이 조각 합
				int dir = bits[r][c] == '0' ? 0 : 1;		//	종이 조각 방향
				
				for(int d = 1; ; d++) {
					int nr = r + dy[dir] * d;
					int nc = c + dx[dir] * d;	//	현재 종이 조각에 포함시킬 다음 칸
					
					//	다음 칸이 종이 범위 밖이거나, 현재 종이 조각 방향과 어긋나거나, 이미 다른 종이 조각에 포함된 경우
					if(!isIn(nr, nc) || bits[nr][nc] != bits[r][c] || visited[nr][nc])
						break;
					
					visited[nr][nc] = true;
					
					partSum *= 10;
					partSum += (board[nr][nc] - '0');
				}
				
				sum += partSum;
			}
		}
		
		return sum;
	}
	
	private static final int[] dy = {0, 1};
	private static final int[] dx = {1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```

**Bruteforcing + Backtracking**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;

	private static int N;
	private static int M;

	private static int ans;

	private static int[][] board;
	private static boolean[][] visited;

	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());

		N = Integer.parseInt(tokens.nextToken());
		M = Integer.parseInt(tokens.nextToken());

		board = new int[N][M];
		visited = new boolean[N][M];

		for (int i = 0; i < N; ++i) {
			String row = br.readLine();

			for (int j = 0; j < M; ++j)
				board[i][j] = row.charAt(j) - '0';
		}

		dfs(0, 0);

		System.out.println(ans);
	} // main-end

	private static void dfs(int nth, int sum) {
		if (nth == N * M) {
			ans = Math.max(sum, ans);
			return;
		}

		int r = nth / M;
		int c = nth % M;

		if (visited[r][c]) {	//	이미 (r, c) 종이 조각이 다른 종이 조각에 포함됐을 경우
			dfs(nth + 1, sum);
			return;
		}

		int partSum = board[r][c];	//	현재 종이 조각 합
		
		visited[r][c] = true;
		dfs(nth + 1, sum + partSum);	//	현재 종이 조각에는 (r, c)만 포함된 경우
		visited[r][c] = false;

		for (int k = 1; k < N - r; k++) {
			int ny = r + k;
			int nx = c;			//	종이 조각을 아랫 방향으로 확장
			
			partSum *= 10;
			partSum += board[ny][nx];
			
			for (int j = 1; j <= k; j++)
				visited[r + j][nx] = true;	//	아랫 방향 종이 조각 방문 처리
			dfs(nth + 1, sum + partSum);
			
			for (int j = 1; j <= k; j++)
				visited[r + j][nx] = false;	//	아랫 방향 종이 조각 방문 처리 해제
		}
		
		partSum = board[r][c];
	
		for (int k = 1; k < M - c; k++) {
			int ny = r;
			int nx = c + k;	//	종이 조각을 오른쪽 방향으로 확장
			
			if (visited[ny][nx])
				return;
			
			partSum *= 10;
			partSum += board[ny][nx];
			
			for (int j = 1; j <= k; j++)
				visited[ny][c + j] = true;	//	오른쪽 방향 종이 조각 방문 처리
			dfs(nth + 1, sum + partSum);
			
			for (int j = 1; j <= k; j++)
				visited[ny][c + j] = false;	//	오른쪽 방향 종이 조각 방문 처리 해제
		}

		visited[r][c] = false;
	}
}	//	Main-class-end
```