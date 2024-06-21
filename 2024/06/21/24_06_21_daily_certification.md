# 24_06_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 31404 아리스, 청소합니다! (Easy)

[](https://www.acmicpc.net/problem/31404)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int H;	//	격자 세로 크기
	private static int W;	//	격자 가로 크기
	private static int row;
	private static int col;	//	아리스 위치
	private static int dir;	//	아리스가 바라보는 방향
	
	private static char[][] ruleA;
	private static char[][] ruleB;	//	규칙표 A, B
	
	private static boolean[][] cleaned;	//	cleaned[i][j] : (i, j)칸이 청소됐으면 true
	private static int[][][] visited;	//	visited[d][i][j] : (i, j)칸에 먼지가 제거된 상태에서 d 방향으로 방문했을때 청소한 칸의 개수
	
	private static int ans = 0;		//	아리스가 이동한 칸 수
	private static int clear = 0;	//	아리스가 청소한 칸 수
	private static int dummy = 0;	//	무의미하게 이동한 칸 수
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		H = Integer.parseInt(tokens.nextToken());
		W = Integer.parseInt(tokens.nextToken());
		
		tokens = new StringTokenizer(br.readLine());
		row = Integer.parseInt(tokens.nextToken());
		col = Integer.parseInt(tokens.nextToken());
		dir = Integer.parseInt(tokens.nextToken());
		
		ruleA = new char[H][W];
		ruleB = new char[H][W];
		cleaned = new boolean[H][W];
		visited = new int[4][H][W];
		
		for(int i = 0; i < H; i++)
			ruleA[i] = br.readLine().toCharArray();
		for(int i = 0; i < H; i++)
			ruleB[i] = br.readLine().toCharArray();
		
		while(cleanUp());
		
		System.out.println(ans - dummy);
	}	//	main-end
	
	//	아리스가 현재 칸을 청소하고 다음 칸으로 이동함
	//	만약 다음 칸이 범위를 벗어날 경우 false 반환
	//	만약 다음 칸에 먼지가 없고 이미 방문한 적이 있는 칸일 경우 false 반환
	//	그 외에는 true 반환
	private static boolean cleanUp() {
		if(!cleaned[row][col]) {	//	현재 칸이 먼지가 있을 경우
			cleaned[row][col] = true;	//	현재 칸 먼지 청소
			turn(ruleA[row][col] - '0');	
			clear++;
			dummy = 0;	//	먼지가 있는 칸을 발견했으므로 이전에 무의미하게 이동한 칸 수 0으로 초기화
		}
		else {
			turn(ruleB[row][col] - '0');
			
			if(visited[dir][row][col] == clear)	//	이전에 방문했을 때랑 방향도 같고 청소 상태도 같을 경우 무의미한 방문
				return false;
			
			visited[dir][row][col] = clear;
			dummy++;	//	먼지 없는 칸 방문하면 무의미한 이동일 수 있음
		}
		
		row += dy[dir];
		col += dx[dir];
		ans++;	//	이동 횟수 1 증가
		
		if(!isIn(row, col))	//	다음 칸이 범위 밖일 경우
			return false;
		
		return true;
	}
	
	//	아리스가 시계 방향으로 cnt번 회전함
	private static void turn(int cnt) {
		dir = (dir + cnt) % 4;
	}
	
	//	위, 왼쪽, 아래, 오른쪽
	private static final int[] dy = {-1, 0, 1, 0};
	private static final int[] dx = {0, 1, 0, -1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < H) && (0 <= x && x < W);
	}
}	//	Main-class-end
```