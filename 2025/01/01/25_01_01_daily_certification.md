# 25_01_01_daily_certification

```
[#001 koreii] 데일리인증 20250101
0. 2025년 목표 설정
- 1월 주요 과제 선정
1. BE 프로젝트 리팩토링
- 프로젝트 구조 파악
  - 주요 도메인 파악
2. 코딩 테스트 대비 알고리즘 학습
- Implementation, Simulation (BOJ 21610 마법사 상어와 비바라기)
- Prefix Sum, imos (BOJ 17037 Painting the Barn (Silver))
```

### **BOJ 21610 마법사 상어와 비바라기**

[21610번: 마법사 상어와 비바라기](https://boj.ma/21610/t)

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
	private static int M;
	
	private static int[][] map;
	private static int[][] copyArr;
	private static boolean[][] isCloud;	//	구름이 있던 칸
	private static final List<int[]> clouds = new ArrayList<>();
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {

		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		isCloud = new boolean[N][N];
		copyArr = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				map[y][x] = Integer.parseInt(st.nextToken());
		}
		
		//	최초 구름
		clouds.add(new int[] {N - 2, 0});
		clouds.add(new int[] {N - 2, 1});
		clouds.add(new int[] {N - 1, 0});
		clouds.add(new int[] {N - 1, 1});
		
		for(int q = 0; q < M; q++) {
			st = new StringTokenizer(br.readLine());
			int d = Integer.parseInt(st.nextToken()) - 1;
			int s = Integer.parseInt(st.nextToken());
			
			move(d, s);
			rain();
			copy();
			makeCloud();
		}
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				answer += map[y][x];
		}
		
		System.out.println(answer);
	}	//	main-end
	
	//	step 1. 모든 구름이 d 방향으로 s칸 이동
	private static void move(int d, int s) {
		for(int[] cloud : clouds) {
			int cy = cloud[0];
			int cx = cloud[1];	//	이동 전 구름 칸
			
			cloud[0] = (cy + dy[d] * s + N * s) % N;
			cloud[1] = (cx + dx[d] * s + N * s) % N;	//	이동 후 구름 칸
		}
	}
	
	//	step 2. 구름 있는 칸 물의 양 1 증가
	private static void rain() {
		for(int[] cloud : clouds) {
			int cy = cloud[0];
			int cx = cloud[1];	//	구름 있던 칸
			
			map[cy][cx]++;	//	구름 있던 칸 비 내려서 물의 양 1 증가
			isCloud[cy][cx] = true;	//	구름 있던 칸 표시
		}
	}
	
	//	step 4. 물복사
	private static void copy() {
		for(int[] pos : clouds) {
			int y = pos[0];
			int x = pos[1];
			
			int cnt = 0;
			for(int d = 1; d < 8; d += 2) {
				int ny = y + dy[d];
				int nx = x + dx[d];
				
				if(isIn(ny, nx) && map[ny][nx] >= 1)
					cnt++;
			}
			
			copyArr[y][x] += cnt;
		}
		
		for(int[] pos : clouds) {
			int y = pos[0];
			int x = pos[1];
			
			map[y][x] += copyArr[y][x];
		}
	}
	
	//	step 5. 구름 생성 + 초기화
	private static void makeCloud() {
		clouds.clear();
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++) {
				//	물의 양이 2 이상이고 이전에 구름이 있던 칸이 아닐 경우
				if(map[y][x] >= 2 && !isCloud[y][x]) {
					map[y][x] -= 2;
					clouds.add(new int[] {y, x});
				}
				
				isCloud[y][x] = false;	//	구름 체크 배열 초기화
				copyArr[y][x] = 0;		//	복사 배열 초기화
			}
		}
	}
	
	private static final int[] dy = {0, -1, -1, -1, 0, 1, 1, 1};
	private static final int[] dx = {-1, -1, 0, 1, 1, 1, 0, -1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```

### **BOJ 17037 Painting the Barn (Silver)**

[17037번: Painting the Barn (Silver)](https://boj.ma/17037/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_002;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int K;

	private static final int[][] prefixSum = new int[MAX][MAX];
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		for(int q = 0; q < N; q++) {
			st = new StringTokenizer(br.readLine());
			int x1 = Integer.parseInt(st.nextToken()) + 1;
			int y1 = Integer.parseInt(st.nextToken()) + 1;
			int x2 = Integer.parseInt(st.nextToken()) + 1;
			int y2 = Integer.parseInt(st.nextToken()) + 1;
			
			prefixSum[x1][y1]++;
			prefixSum[x2][y1]--;
			prefixSum[x1][y2]--;
			prefixSum[x2][y2]++;
		}
		
		for(int y = 1; y < MAX - 1; y++) {
			for(int x = 1; x < MAX - 1; x++) {
				prefixSum[y][x] = prefixSum[y - 1][x] + prefixSum[y][x - 1] - prefixSum[y - 1][x - 1] + prefixSum[y][x];
				
				if(prefixSum[y][x] == K)
					answer++;
			}
		}
		
		System.out.println(answer);
	}	//	main-end
}	//	Main-class-end
```