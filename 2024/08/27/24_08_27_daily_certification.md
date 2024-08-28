# 24_08_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15949 Piet

[](https://www.acmicpc.net/problem/15949)

```java
import java.util.*;
import java.io.*;

public class Main {
	private static final char NONE = 'X';
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	세로 길이
	private static int M;	//	가로 길이
	
	private static char[][] piet;	//	piet 코드
	
	private static int DP = 2;			//	초기 DP는 오른쪽 (2)
	private static char CCD = 'L';		//	초기 CC 상대적 방향은 왼쪽 방향
	private static int CC = 1;			//	초기 CC 실제 방향은 위쪽 (1)
	
	private static int RDP = reverseDir(DP);	//	현재 DP의 역방향
	private static int RCC = reverseDir(CC);	//	현재 CC의 역방향
	
	private static int blockCnt = 0;		//	piet 코드에 존재하는 블록 개수	
	private static boolean[][] visited;		//	방문 처리 배열
	
	private static int curY = 0;
	private static int curX = 0;		//	현재 블록 시작 위치

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		piet = new char[N][M];
		for(int r = 0; r < N; r++)
			piet[r] = br.readLine().toCharArray();

		visited = new boolean[N][M];
		
		while(simulation());
		
		System.out.print(sb);
	}	//	main-end
	
	
	private static boolean simulation() {
		boolean find = false;	//	가능한 DP, CC를 찾지 못하면 false
		
		sb.append(piet[curY][curX]);
		
		for(int t = 0; t < 8; t++) {
			//	현재 블록에서 DP 방향으로 가장 끝에 있는 코델들 찾기, 그 코델들 중에서 CC 방향으로 가장 끝에 있는 코델 찾기
			int[] pos = bfs();
			
			int ny = pos[0] + dy[DP];
			int nx = pos[1] + dx[DP];	//	해당 코델에서 DP 방향으로 맞닿은 다른 블록 좌표
			
			if(isIn(ny, nx) && piet[ny][nx] != NONE) {	//	DP로 맞닿은 칸이 범위 안이고 'X'가 아닐 경우
				find = true;
				
				curY = ny;
				curX = nx;	//	다음 시작은 (ny, nx)에서부터 한다.
				
				break;
			}
			
			if(t % 2 == 0)	//	CC 상대방향을 바꿔보기
				CCD = CCD == 'L' ? 'R' : 'L';
			else	//	CC 상대방향 유지하고 DP 시계방향으로 회전하기
				DP = (DP + 1) % 4;
			
			setCC();	//	CC 절대방향 구하기
			RDP = reverseDir(DP);
			RCC = reverseDir(CC);
		}

		return find;
	}
	
	private static int[] bfs() {
		Queue<int[]> q = new LinkedList<>();	//	{y, x, dpDist, ccDist} : (y, x), (y, x)가 (curY, curX) 기준으로 DP 방향으로 떨어진 거리, CC 방향으로 떨어진 거리 
		
		reset();	//	방문 초기화
		
		int maxDPDist = 0;	//	(curY, curX) 기준 DP 방향으로 가장 멀리 떨어진 코델의 DP 방향 거리
		int maxCCDist = 0;	//	(curY, curX) 기준 DP 방향으로 가장 멀리 떨어진 코델들 중 CC 방향으로 가장 멀리 떨어진 코델의 CC 방향 거리
		int maxY = curY;
		int maxX = curX;	//	(curY, curX) 기준 DP 방향으로 가장 멀리 떨어진 코델들 중, CC 방향으로 가장 멀리 떨어진 코델의 좌표
		
		visited[curY][curX] = true;
		q.offer(new int[] {curY, curX, 0, 0});
		
		while(!q.isEmpty()) {
			int[] now = q.poll();
			int nowY = now[0];
			int nowX = now[1];			//	현재 코델 좌표
			int nowDPDist = now[2];		//	현재 코델이 (curY, curX) 기준으로 DP 방향으로 떨어진 거리
			int nowCCDist = now[3];		//	현재 코델이 (curY, curX) 기준으로 CC 방향으로 떨어진 거리
			
			for(int d = 0; d < 4; d++) {
				int nextY = nowY + dy[d];
				int nextX = nowX + dx[d];	//	다음 코델 좌표
				int nextDPDist = nowDPDist;	//	다음 코델이 (curY, curX) 기준으로 DP 방향으로 떨어진 거리
				int nextCCDist = nowCCDist; //	다음 코델이 (curY, curX) 기준으로 CC 방향으로 떨어진 거리
				
				if(isIn(nextY, nextX) && piet[nextY][nextX] == piet[curY][curX] && !visited[nextY][nextX]) {	//	다음 코델이 범위 밖이 아니고, 'X'도 아니며, 아직 방문 안했을 경우
					nextDPDist += (d == RDP) ? -1 : ((d == DP) ? 1 : 0);	//	d에 따라 nextDPDist 조정
					nextCCDist += (d == RCC) ? -1 : ((d == CC) ? 1 : 0);	//	d에 따라 nextCCDist 조정
					
					if(nextDPDist > maxDPDist) {	//	DP 방향으로 가장 먼 코델을 찾았을 경우
						maxDPDist = nextDPDist;
						maxCCDist = nextCCDist;
						maxY = nextY;
						maxX = nextX;
					}
					else if(nextDPDist == maxDPDist && nextCCDist > maxCCDist) {	//	DP 방향으로 거리는 최대 거리로 같고, CC 방향으로 가장 먼 코델을 찾았을 경우
						maxCCDist = nextCCDist;
						maxY = nextY;
						maxX = nextX;
					}
					
					visited[nextY][nextX] = true;	//	방문 처리
					q.offer(new int[] {nextY, nextX, nextDPDist, nextCCDist});
				}
			}
		}	//	while-end
	
		return new int[] {maxY, maxX};
	}	//	bfs-end
	
	private static void reset() {
		for(int r = 0; r < N; r++)
			Arrays.fill(visited[r], false);
	}

	private static int[] dy = {0, -1, 0, 1};
	private static int[] dx = {-1, 0, 1, 0};
	
	private static void setCC() {	//	현재 DP값과 상대적인 CP 값에 따른 실제 CP 값
		switch(DP) {
		case 0:
			CC = CCD == 'L' ? 3 : 1;
			break;
		case 1:
			CC = CCD == 'L' ? 0 : 2;
			break;
		case 2:
			CC = CCD == 'L' ? 1 : 3;
			break;
		default:
			CC = CCD == 'L' ? 2 : 0;
		}
	}
	
	//	dir의 반대 방향 반환
	private static int reverseDir(int dir) {
		if(dir == 0 || dir == 2)
			return 2 - dir;
		else
			return 4 - dir;
	}
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```