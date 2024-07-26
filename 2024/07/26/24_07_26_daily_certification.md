# 24_07_26_daily_certification

# Problem Solving (Algorithm & SQL)

### 삼성전자 2021년 상반기 오후 2번 미로 타워 디펜스 (Success)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/maze-tower-defense/description?page=2&pageSize=20)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int EMPTY = 0;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;			//	맵 크기 N * N
	private static int M;			//	라운드 수
	private static int MCNT;		//	최대 몬스터 수
	private static int CY;			//	타워 y 좌표
	private static int CX;			//	타워 X 좌표
	
	private static int[][] map;		//	맵
	
	private static int[] monsters;			//	몬스터 리스트
	private static int[][] coordToIdx;		//	coordToIdx[y][x] : map의 좌표 (y, x)에 있는 몬스터의 monsters에서의 index
	private static int[][] idxToCoord;		//	idxToCoord[idx] : monsters의 idx에 위치한 몬스터의 map에서의 좌표 {y, x}
	private static boolean[][] visited;		//	달팽이 배열 만들기 위한 방문 처리 배열
	
	private static int ans = 0;				//	플레이어가 얻은 총 점수
	
	public static void main(String[] args) throws IOException {
		init();
		simulation();
		
		System.out.println(ans);
	}	//	main-end
	
	private static void init() throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		MCNT = N * N - 1;
		
		CY = N / 2;
		CX = N / 2;
		
		map = new int[N][N];
		monsters = new int[MCNT];
		coordToIdx = new int[N][N];
		idxToCoord = new int[MCNT][2];
		visited = new boolean[N][N];
		
		int cur = MCNT - 1;
		int cy = 0;
		int cx = 0;
		int cdir = 0;
		
		coordToIdx[cy][cx] = cur;
		idxToCoord[cur] = new int[] {cy, cx};
		visited[cy][cx] = true;
		cur--;
		
		while(cur >= 0) {
			int ndir = cdir;
			int ny = cy + dy[ndir];
			int nx = cx + dx[ndir];
			
			while(!isIn(ny, nx) || visited[ny][nx]) {
				ndir = (ndir + 1) % 4;
				ny = cy + dy[ndir];
				nx = cx + dx[ndir];
			}
			
			cy = ny;
			cx = nx;
			cdir = ndir;
			coordToIdx[cy][cx] = cur;
			idxToCoord[cur] = new int[] {cy, cx};
			visited[cy][cx] = true;
			cur--;
		}
		
		for(int r = 0; r < N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < N; c++) {
				int monster = Integer.parseInt(st.nextToken());
				map[r][c] = monster;
				
				if(monster > 0) {	//	1, 2, 3 몬스터일 경우
					int idx = coordToIdx[r][c];	//	해당 몬스터의 monsters에서의 위치
					monsters[idx] = monster;
				}
			}
		}
	}
	
	private static void simulation() throws IOException {	
		for(int t = 0; t < M; t++) {
			st = new StringTokenizer(br.readLine());
			int d = Integer.parseInt(st.nextToken());	//	(CY, CX)로부터 d 방향으로	
			int p = Integer.parseInt(st.nextToken());	//	p칸만큼 공격하기
			
			step1(d, p);
			int lastIdx = step23();
			step4(lastIdx);
		}
	}
	
	//	step 1. 타워에서 d 방향으로 p칸만큼 제거
	private static void step1(int d, int p) {
		for(int k = 1; k <= p; k++) {
			int ny = CY + dy[d] * k;
			int nx = CX + dx[d] * k;
			
			ans += map[ny][nx];
			map[ny][nx] = EMPTY;
			monsters[coordToIdx[ny][nx]] = EMPTY;
		}
	}
	
	private static int step23() {
		int lastIdx = 0;	//	step 2, 3을 거쳤을 때 마지막 몬스터의 index
		
		while(true) {
			//	step 2. 몬스터를 앞으로 당김
			int idx = 0;
			int[] tmp = new int[MCNT];
		
			for(int i = 0; i < MCNT; i++) {
				if(monsters[i] != 0)
					tmp[idx++] = monsters[i];
			}
			
			//	step 3. 4개 이상 연속한 같은 몬스터를 제거함
			int limit = idx;	//	당겼을 때 마지막 몬스터의 다음 위치
			int start = 0;
			int end = 0;
			boolean changed = false;	//	연속한 몬스터들이 제거된 적이 있는지 확인
			
			while(end < limit) {
				while(end + 1 < limit && tmp[end + 1] == tmp[start])
					end++;
				
				int cnt = end - start + 1;	//	연속된 같은 몬스터 개수
				
				if(cnt >= 4) {	//	연속된 같은 몬스터 개수가 4개 이상일 경우
					changed = true;
					for(int i = start; i <= end; i++) {
						ans += tmp[i];
						tmp[i] = EMPTY;
					}
				}
				
				start = end + 1;
				end = start;
			}
			
			monsters = tmp;	//	monsters 갱신
			
			if(!changed) {	//	연속한 몬스터들이 제거된 적이 없을 경우, 다시 당길 필요 없음
				lastIdx = limit - 1;	//	마지막 몬스터의 index를 반환
				break;
			}
		}
		
		return lastIdx;
	}
	
	private static void step4(int lastIdx) {
		List<Integer> addMonsters = new ArrayList<>();	//	추가될 몬스터
		
		int start = 0;
		int end = 0;
		
		while(end <= lastIdx) {
			while(end + 1 <= lastIdx && monsters[end + 1] == monsters[start])
				end++;
			
			int cnt = end - start + 1;	//	연속된 같은 몬스터 개수
			int num = monsters[end];	//	연속된 몬스터 번호
			
			addMonsters.add(cnt);
			addMonsters.add(num);
			
			start = end + 1;
			end = start;
		}
			
		Arrays.fill(monsters, EMPTY);
		
		for(int i = 0; i < Math.min(MCNT, addMonsters.size()); i++)
			monsters[i] = addMonsters.get(i);
		
		redraw();
	}
	
	//	step 1 ~ 4 마친 후 map 다시 그리기
	private static void redraw() {
		for(int idx = 0; idx < MCNT; idx++) {
			int monster = monsters[idx];
			int y = idxToCoord[idx][0];
			int x = idxToCoord[idx][1];
			
			map[y][x] = monster;
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};	//	방향 벡터
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```