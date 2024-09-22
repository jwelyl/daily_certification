# 24_09_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23289 온풍기 안녕!

[](https://www.acmicpc.net/problem/23289)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.Queue;
import java.util.LinkedList;
import java.util.ArrayList;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int R;
	private static int C;	//	맵 크기
	private static int K;	//	목표 온도
	private static int W;	//	벽의 개수
	
	private static int[][] temperatures;	//	각 칸 온도
	
	private static boolean[][][][] walls;	//	walls[y1][x1][y2][x2] :  (y1, x1)와 (y2, x2) 사이에 벽이 존재하면 true
	
	private static List<int[]> heaters = new ArrayList<>();			//	히터 모음 {num, y, x, d}
	private static List<int[]> checkPoints = new ArrayList<>();		//	온도 확인해야 하는 칸 모음 {y, x}
	
	private static int hNum = 0;	//	히터 번호
	private static int time = 1;	//	시뮬레이션 시간, 시간 1 당 초콜릿 한 개 먹음
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		temperatures = new int[R][C];
		walls = new boolean[R][C][R][C];
		
		for(int r = 0; r < R; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < C; c++) {
				int num = Integer.parseInt(st.nextToken());
				
				if(num == 5)	//	(r, c)가 온도를 확인해야 하는 칸이면
					checkPoints.add(new int[] {r, c});
				else if(1 <= num && num <= 4) 	//	온풍기이면
					heaters.add(new int[] {++hNum, r, c, num - 1});
			}
		}
		
		W = Integer.parseInt(br.readLine());
		
		for(int w = 0; w < W; w++) {
			st = new StringTokenizer(br.readLine());
			
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;	//	벽 위치
			int t = Integer.parseInt(st.nextToken());		//	벽 종류
			
			if(t == 0) {	//	(y, x)와, (y - 1, x) 사이에 벽이 생김
				walls[y][x][y - 1][x] = true;
				walls[y - 1][x][y][x] = true;
			}
			else {	//	(y, x)와 (y, x + 1) 사이에 벽 생김
				walls[y][x][y][x + 1] = true;
				walls[y][x + 1][y][x] = true;
			}
		}
		
		for(; time <= 100; time++) {
			step1();	//	모든 온풍기에서 바람이 한 번씩 나옴
			step2();	//	온도가 조절됨
			step3();	//	바깥쪽 칸의 온도가 1씩 감소
			
			if(isEnd())
				break;
		}
		
		System.out.println(time);
	}	//	main-end
	
	private static void step1() {
		int[][] add = new int[R][C];		//	add[r][c] : (r, c)칸 온도의 최종 변화량
		int[][] visited = new int[R][C];	//	각 칸에 영향을 준 온풍기 번호, 0이면 어떤 온풍기도 영향을 주지 않음
		
		for(int[] heater : heaters)	//	모든 히터가 바람을 한 번 뱉음
			bfs(heater, add, visited);
		
		updateTemperatures(add);
	}
	
	private static void bfs(int[] heater, int[][] add, int[][] visited) {
		Queue<int[]> q = new LinkedList<>();
		int h = heater[0];	//	온풍기 번호
		int y = heater[1];
		int x = heater[2];	//	온풍기 위치
		int d = heater[3];	//	온풍기가 바람 뱉는 방향
		
		int[][] tmp = new int[R][C];
		
		tmp[y + wDy[d][1]][x + wDx[d][1]] = 5;
		add[y + wDy[d][1]][x + wDx[d][1]] += 5;
		visited[y + wDy[d][1]][x + wDx[d][1]] = h;
		q.offer(new int[] {y + wDy[d][1], x + wDx[d][1], 5});
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];		//	(cy, cx)칸
			int cinc = cur[2];		//	(cy, cx)칸 온도 증가량
			
			if(cinc == 1)	//	해당 칸에서 더 바람 나갈 수 없음
				continue;
			
			for(int i = 0; i < 3; i++) {
				int ny = cy + wDy[d][i];
				int nx = cx + wDx[d][i];	//	다음 칸	
				int ninc = cinc - 1;	//	다음 칸 온도 증가량
				
				//	해당 칸이 격자 안에 있고, (cy, cx)에서 (ny, nx)로 이동 가능하며, (ny, nx)가 h에 의해 영향 받은 적이 없을 경우
				if(isIn(ny, nx) && canMove(d, cy, cx, ny, nx) && visited[ny][nx] != h) {
					add[ny][nx] += ninc;	//	(ny, nx)칸 온도 ninc만큼 증가
					tmp[ny][nx] = ninc;
					visited[ny][nx] = h;
					q.offer(new int[] {ny, nx, ninc});
				}
			}
		}	//	while-end
	}	//	bfs-end
	
	//	바람의 방향이 dir일때, (fy, fx)에서 (ty, tx)로 이동 가능하면 true
	private static boolean canMove(int dir, int fy, int fx, int ty, int tx) {
		if(dir == 0 || dir == 1) {	//	오른쪽 또는 왼쪽일 경우
			if(ty == fy) {
				if(walls[fy][fx][ty][tx])
					return false;
			}
			else {
				if(walls[fy][fx][ty][fx] || walls[ty][fx][ty][tx])
					return false;
			}
		}
		else {	//	위쪽 또는 아래쪽일 경우
			if(tx == fx) {
				if(walls[fy][fx][ty][tx])
					return false;
			}
			else {
				if(walls[fy][fx][fy][tx] || walls[fy][tx][ty][tx])
					return false;
			}
		}
		
		return true;
	}
	
	private static void step2() {
		int[][] differences = new int[R][C];
		
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int cy = r;
				int cx = c;	//	현재 칸
				
				for(int d = 0; d < 2; d++) {	//	오른쪽, 아래쪽 인접한 칸 확인 (중복 확인 방지)
					int ny = cy + dy[d];
					int nx = cx + dx[d];	//	인접한 칸
					
					//	인접한 칸이 격자 안에 있고, 둘 사이에 벽이 없을 경우, 둘 사이에 온도 조절 일어남
					if(isIn(ny, nx) && !walls[cy][cx][ny][nx]) {
						int diff = Math.abs(temperatures[ny][nx] - temperatures[cy][cx]);
						
						if(diff != 0) {	//	온도 조절이 필요할 경우
							if(temperatures[ny][nx] > temperatures[cy][cx]) {	//	(ny, nx)칸의 온도가 더 높을 경우
								differences[ny][nx] -= (diff / 4);
								differences[cy][cx] += (diff / 4);
							}
							else {	//	(cy, cx)칸의 온도가 더 높을 경우
								differences[ny][nx] += (diff / 4);
								differences[cy][cx] -= (diff / 4);
							}
						}
					}
				}
			}
		}
	
		updateTemperatures(differences);
	}
	
	private static void updateTemperatures(int[][] diff) {
		//	각 칸별로 온도 변화량 반영
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++)
				temperatures[r][c] += diff[r][c];
		}
	}
 	
	private static void step3() {
		for(int c = 0; c < C; c++) {
			if(temperatures[0][c] > 0)
				temperatures[0][c]--;
			if(temperatures[R - 1][c] > 0)
				temperatures[R - 1][c]--;
		}
		
		for(int r = 1; r < R - 1; r++) {
			if(temperatures[r][0] > 0)
				temperatures[r][0]--;
			if(temperatures[r][C - 1] > 0)
				temperatures[r][C - 1]--;
		}
	}
	
	//	온도를 확인해야 하는 칸의 온도가 모두 K개 이상인지 체크
	private static boolean isEnd() {
		for(int[] checkPoint : checkPoints) {
			int y = checkPoint[0];
			int x = checkPoint[1];
			
			if(temperatures[y][x] < K)	//	한 칸이라도 K도 이하일 경우
				return false;
		}
		
		return true;
	}

	private static final int[][] wDy = {{-1, 0, 1}, {-1, 0, 1}, {-1, -1, -1}, {1, 1, 1}};
	private static final int[][] wDx = {{1, 1, 1}, {-1, -1, -1}, {-1, 0, 1}, {-1, 0, 1}};	//	온풍기 방향에 따른 바람 퍼지는 방향
	
	private static final int[] dy = {0, 1};
	private static final int[] dx = {1, 0};	//	인접한 두 방향 (중복 방지)
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < R) && (0 <= x && x < C);
	}
}	//	Main-class-end
```

### BOJ 19942 다이어트

[](https://www.acmicpc.net/problem/19942)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final int NONE = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    식재료 개수
    private static int mp;
    private static int mf;
    private static int ms;
    private static int mv;    //    필수 영양소 요구량
    
    private static int[] p;
    private static int[] f;
    private static int[] s;
    private static int[] v;    //    식재료별 영양소
    private static int[] c;    //    식재료별 가격
    
    private static int minCost = NONE;    	//    만족하는 식재료 조합 중 최소 가격, 만족하는 식재료 조합이 없을 경우 NONE
    private static String minCombination = "";
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        mp = Integer.parseInt(st.nextToken());
        mf = Integer.parseInt(st.nextToken());
        ms = Integer.parseInt(st.nextToken());
        mv = Integer.parseInt(st.nextToken());
        
        p = new int[N];
        f = new int[N];
        s = new int[N];
        v = new int[N];
        c = new int[N];
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            p[i] = Integer.parseInt(st.nextToken());
            f[i] = Integer.parseInt(st.nextToken());
            s[i] = Integer.parseInt(st.nextToken());
            v[i] = Integer.parseInt(st.nextToken());
            c[i] = Integer.parseInt(st.nextToken());
        }
        
        combination(0, 0, 0, 0, 0, 0, new StringBuilder());
        
        if(minCost == NONE)
            System.out.println(-1);
        else {
            sb.append(minCost).append("\n");
            sb.append(minCombination);
            System.out.println(sb);
        }
    }    //    main-end
    
    private static void combination(int start, int ps, int fs, int ss, int vs, int cs, StringBuilder combination) {
        if(ps >= mp && fs >= mf && ss >= ms && vs >= mv) {	//	조건을 만족할 경우
        	if(cs < minCost) {
        		minCost = cs;
        		minCombination = combination.toString();
        	}
        	
        	return;
        }
    	
    	for(int i = start; i < N; i++) {
    		StringBuilder ncombination = new StringBuilder(combination);
    		ncombination.append(i + 1).append(" ");
    		combination(i + 1, ps + p[i], fs + f[i], ss + s[i], vs + v[i], cs + c[i], ncombination);
    	}
    }
}    //    Main-class-end
```