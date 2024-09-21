# 24_09_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23290 마법사 상어와 복제

[](https://www.acmicpc.net/problem/23290)

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
	
	private static int M;	//	초기 격자에 들어있는 물고기 수
	private static int S;	//	마법 횟수 S
	
	private static List<Fish>[][] fishMap = new ArrayList[5][5];	//	fishMap[y][x] : (y, x)칸에 존재하는 살아있는 물고기 리스트
	private static List<Fish>[][] moveMap = new ArrayList[5][5];	//	물고기 이동을 위한 임시 맵
 	private static final int[][] smellMap = new int[5][5];	//	(y, x)칸의 물고기가 상어에게 잡혀 쫓겨날 때 냄새를 남긴 시간 
	private static final List<Fish> copiedFishList = new ArrayList<>();	//	복제된 물고기들 저장 리스트
	
	private static int sy;
	private static int sx;	//	상어 위치
	
	private static int maxFishCnt = -1;					//	상어가 3단계에서 이동할 때 잡을 수 있는 물고기 수의 최대
	private static int[] maxFishCntSelected = null;		//	그때 상어가 이동하는 방법
	
	public static void main(String[] args) throws IOException {
		for(int y = 1; y <= 4; y++) {
			for(int x = 1; x <= 4; x++) {
				fishMap[y][x] = new ArrayList<>();
				moveMap[y][x] = new ArrayList<>();
			}
		}
		
		st = new StringTokenizer(br.readLine());
		M = Integer.parseInt(st.nextToken());
		S = Integer.parseInt(st.nextToken());
		
		for(int m = 1; m <= M; m++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken()) - 1;
			
			fishMap[y][x].add(new Fish(y, x, d));	//	최초 물고기 M마리 배치
		}
		
		st = new StringTokenizer(br.readLine());
		sy= Integer.parseInt(st.nextToken());
		sx = Integer.parseInt(st.nextToken());	//	초기 상어 위치
	
		simulation();
		
		System.out.println(countFish());
	}	//	main-end
	
	private static void simulation() {
		for(int time = 1; time <= S; time++) {
			//	1단계. fishMap의 모든 물고기 복제 시전
			copyFish();
			//	2단계. fishMap의 모든 물고기가 한 칸씩 이동
			moveAllFish(time);
			//	3단계. 상어가 3칸 이동함
			moveShark(time);
			//	5단계. 복제가 완료됨
			completeCopy();
		}
	}
	
	//	1단계.
	private static void copyFish() {
		//	현재 fishMap에 존재하는 모든 물고기를 복사하여 copiedFishList에 저장 
		for(int y = 1; y <= 4; y++) {
			for(int x = 1; x <= 4; x++) {
				for(Fish origFish : fishMap[y][x]) {
					Fish copiedFish = new Fish(origFish);
					copiedFishList.add(copiedFish);
				}
			}
		}
	}
	
	//	2단계.
	private static void moveAllFish(int time) {
		for(int y = 1; y <= 4; y++) {
			for(int x = 1; x <= 4; x++) {	//	(y, x)칸에 있는 물고기를 이동시키기
				for(Fish fish : fishMap[y][x]) {	//	이동시킬 물고기 fish
					int cy = fish.y;
					int cx = fish.x;	//	이동시킬 물고기 현재 위치
					int cd = fish.d;	//	이동시킬 물고기 현재 이동 시도할 방향
					
					int ny = -1;
					int nx = -1;	//	이동시킬 칸
					int nd = -1;	//	이동시킬 방향, 이동시킬 수 있는 방향이 없으면 이동시키지 않음
				
					for(int nth = 0; nth < 8; nth++) {
						ny = cy + fDy[cd];
						nx = cx + fDx[cd];	//	이동시킬 물고기가 이동을 시도할 위치
						
						//	(ny, nx)가 격자를 벗어났거나
						//	(ny, nx)에 상어가 존재하거나
						//	(ny, nx)에 물고기의 냄새가 남아있을 경우
						if(!isIn(ny, nx) || (ny == sy && nx == sx) || (smellMap[ny][nx] != 0 && time - smellMap[ny][nx] <= 2)) {
							cd = (cd == 0) ? 7 : cd - 1;	//	다음 방향으로 이동을 시도해보기
							continue;
						}
						
						nd = cd;	//	이동 방향 정해짐
						break;
					}
					
					if(nd != -1) {	//	이동할 수 있을 경우
						fish.y = ny;
						fish.x = nx;
						fish.d = nd;	//	이동시킬 물고기의 위치, 방향 변화
						moveMap[ny][nx].add(fish);	//	임시 맵의 (ny, nx)에 이동시킨 물고기 저장
					}
					else	//	이동할 수 없을 경우
						moveMap[cy][cx].add(fish);	//	임시 맵의 현재 위치에 그대로 물고기 저장	
				}
			}
		}
		
		for(int y = 1; y <= 4; y++) {
			for(int x = 1; x <= 4; x++)
				fishMap[y][x].clear();
		}
		
		List<Fish>[][] tmp = fishMap;
		fishMap = moveMap;	//	이동 완료된 상태인 moveMap을 fishMap으로 사용
		moveMap = tmp;	//	다음 이동 및 삭제를 위해 moveMap 초기화
	}
	
	//	3단계.
	private static void moveShark(int time) {
		maxFishCnt = -1;					//	상어가 3단계에서 이동할 때 잡을 수 있는 최대 물고기 수 초기화
		maxFishCntSelected = null;			//	그 때의 상어가 이동할 방법
		
		permutation(0, sy, sx, new int[3]);	//	상어가 이동할 방법 찾기
		
		for(int i = 0; i < 3; i++) {
			sy += sDy[maxFishCntSelected[i]];
			sx += sDx[maxFishCntSelected[i]];	//	상어가 이동한 칸
			
			if(fishMap[sy][sx].size() > 0) {	//	해당 칸에 물고기가 있을 경우
				smellMap[sy][sx] = time;	//	냄새 남기기
				fishMap[sy][sx].clear();	//	해당 칸의 물고기 모두 제외시키기
			}
		}
	}
	
	private static void permutation(int nth, int y, int x, int[] selected) {
		if(nth == 3) {	//	3번 이동하는 방법을 정한 경우
			int fishCnt = 0;	//	정한 방법대로 이동했을 때 잡을 수 있는 물고기 수
			int cy = sy;
			int cx = sx;
			
			boolean[][] visited = new boolean[5][5];	//	같은 칸 여려번 방문 시 물고기 중복으로 세지 않도록
		
			for(int i = 0; i < 3; i++) {
				int ny = cy + sDy[selected[i]];
				int nx = cx + sDx[selected[i]];	//	이동할 칸
				
				if(!visited[ny][nx]) {	//	아직 방문하지 않은 칸일 경우
					visited[ny][nx] = true;
					fishCnt += fishMap[ny][nx].size();	//	해당 칸에 있는 물고기 수 추가
				}
				
				cy = ny;
				cx = nx;
			}
			
			if(fishCnt > maxFishCnt) {	//	최대로 잡을 수 있는 물고기 수 갱신된 경우
				maxFishCnt = fishCnt;
				maxFishCntSelected = new int[3];
				for(int i = 0; i < 3; i++)
					maxFishCntSelected[i] = selected[i];
			}
			
			return;
		}
		
		for(int d = 0; d < 4; d++) {
			int ny = y + sDy[d];
			int nx = x + sDx[d];	//	d 방향으로 이동한 칸
			
			if(!isIn(ny, nx))	//	범위 벗어날 경우
				continue;
			
			selected[nth] = d;
			permutation(nth + 1, ny, nx, selected);
		}
	}
	
	//	5단계
	private static void completeCopy() {
		for(Fish copiedFish : copiedFishList) {	//	1단계에서 복제된 물고기
			int y = copiedFish.y;
			int x = copiedFish.x;	//	물고기가 초기 복제되었던 위치
			
			fishMap[y][x].add(copiedFish);	//	해당 위치에 복제된 물고기 배치하기
		}
		
		copiedFishList.clear();	//	리스트 비우기
	}
	
	private static int countFish() {
		int ret = 0;
		
		for(int y = 1; y <= 4; y++)
			for(int x = 1; x <= 4; x++)
				ret += fishMap[y][x].size();
		
		return ret;
	}
	
	private static final int[] fDy = {0, -1, -1, -1, 0, 1, 1, 1};
	private static final int[] fDx = {-1, -1, 0, 1, 1, 1, 0, -1};	//	물고기 이동 8방향
	
	private static final int[] sDy = {-1, 0, 1, 0};
	private static final int[] sDx = {0, -1, 0, 1};	//	상어 이동 4방향
	
	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= 4) && (1 <= x && x <= 4);
	}
	
	private static class Fish {
		public int y;
		public int x;	//	물고기 위치
		public int d;	//	물고기 이동 방향
		
		public Fish(int y, int x, int d) {
			this.y = y;
			this.x = x;
			this.d = d;
		}
		
		public Fish(Fish fish) {	//	물고기 복제를 위한 복사 생성자
			this(fish.y, fish.x, fish.d);
		}
	}
}	//	Main-class-end
```

### BOJ 19951 태상이의 훈련소 생활

[](https://www.acmicpc.net/problem/19951)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //   칸 개수
    private static int M;    //   쿼리 개수
    
    private static int[] heights;        //    각 칸 높이
    private static int[] additions;      //    각 칸마다 더해지는 높이
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        heights = new int[N + 1];
        additions = new int[N + 2];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            heights[i] = Integer.parseInt(st.nextToken());
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int from = Integer.parseInt(st.nextToken());
            int to = Integer.parseInt(st.nextToken());
            int add = Integer.parseInt(st.nextToken());
            
            additions[from] += add;
            additions[to + 1] -= add;
        }
        
        for(int i = 1; i <= N; i++) {
            additions[i] = additions[i - 1] + additions[i];
            sb.append(heights[i] + additions[i]).append(" ");
        }
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```