# 24_12_31_daily_certification

### **BOJ 20057 마법사 상어와 토네이도**

[20057번: 마법사 상어와 토네이도](https://boj.ma/20057/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int[][] snail;
	private static int[][] sand;
	
	private static int[][] pos;	//	pos[num] : 토네이도가 num번 위치일때의 좌표 {y, x}
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		sand = new int[N][N];
		snail = new int[N][N];
		pos = new int[N * N + 1][2];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				sand[y][x] = Integer.parseInt(st.nextToken());
		}
		
		makeSnail();
		
		for(int i = 1; i < N * N; i++)
			move(i, i + 1);
		
		System.out.println(answer);
	}	//	main-end
	
	private static void makeSnail() {
		int order = 0;
		int cy = 0;
		int cx = 0;
		int cdir = 0;
		
		while(true) {
			snail[cy][cx] = N * N - order;
			pos[N * N - order][0] = cy;
			pos[N * N - order][1] = cx;
			order++;
			
			if(order >= N * N)
				break;
			
			while(true) {
				int ny = cy + dy[cdir];
				int nx = cx + dx[cdir];
				
				if(isIn(ny, nx) && snail[ny][nx] == 0) {
					cy = ny;
					cx = nx;
					break;
				}
				
				cdir = (cdir + 1) % 4;
			}
		}
	}

	private static int getDir(int from, int to) {
		int fy = pos[from][0];
		int fx = pos[from][1];
		int ty = pos[to][0];
		int tx = pos[to][1];

		if(fy == ty) {
			if(tx - fx == 1)
				return 0;
			else
				return 2;
		}
		else {
			if(ty - fy == 1)
				return 1;
			else
				return 3;
		}
	}
	
	//	토네이도가 from번에서 to번으로 이동함
	private static void move(int from, int to) {
		int hdir = getDir(from, to);	//	토네이도 이동 방향 (이동방향과 수평 방향)
		int vdir = (hdir + 1) % 4;		//	토네이도 이동과 수직한 방향

		int yy = pos[to][0];
		int yx = pos[to][1];		//	토네이도가 도착할 곳
		int before = sand[yy][yx];	//	해당 위치에 원래 있던 모래 양
		
		int[] hcoef = {-1, -1, 0, 0, 0, 0, 1, 1, 2};		//	토네이도 이동 방향과 수평 벡터 계수
		int[] vcoef = {-1, 1, -2, -1, 1, 2, -1, 1, 0};		//	토네이도 이동 방향과 수직 벡터 계수
		int[] portion = {1, 1, 2, 7, 7, 2, 10, 10, 5};
		
		for(int i = 0; i < hcoef.length; i++) {
			int ny = yy + hcoef[i] * dy[hdir] + vcoef[i] * dy[vdir]; 
			int nx = yx + hcoef[i] * dx[hdir] + vcoef[i] * dx[vdir];
			
			int out = (before * portion[i]) / 100;	//	나가는 양
			sand[yy][yx] -= out;	//	원래 칸에서 모래 빠져나감
			
			if(!isIn(ny, nx))
				answer += out;	//	지도 밖으로 나가는 양
			else
				sand[ny][nx] += out;
		}
		
		int ay = yy + dy[hdir];
		int ax = yx + dx[hdir];	//	남은 모래가 이동할 칸
		
		if(isIn(ay, ax))	//	해당 칸이 맵 안일 경우
			sand[ay][ax] += sand[yy][yx];
		else	//	해당 칸이 맵 밖일 경우
			answer += sand[yy][yx];
		
		sand[yy][yx] = 0;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```