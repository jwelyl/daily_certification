# 24_06_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20005 보스몬스터 전리품

[](https://www.acmicpc.net/problem/20005)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static char[][] map;
	private static int[][] dist;
	
	private static int bossY;
	private static int bossX;		//	보스 위치
	private static int bossHp;		//	보스 HP
	
	private static int M;	//	세로 길이
	private static int N;	//	가로 길이
	private static int P;	//	플레이어 수
	
	private static int[] playerNums;
	private static int[] playerDist;
	private static int[] dps = new int[26];
	private static final int[][] pos = new int[26][2];
	
	private static int ans = 0;
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		M = Integer.parseInt(tokens.nextToken());
		N = Integer.parseInt(tokens.nextToken());
		P = Integer.parseInt(tokens.nextToken());
		
		map = new char[M][N];
		dist = new int[M][N];
		for(int i = 0; i < M; i++)
			Arrays.fill(dist[i], INF);
		playerNums = new int[P];
		playerDist = new int[P];
		
		int p = 0;
		for(int i = 0; i < M; i++) {
			String input = br.readLine();
			for(int j = 0; j < N; j++) {
				char ch = input.charAt(j);
				
				if(ch == 'B') {
					bossY = i;
					bossX = j;
				} else if('a' <= ch && ch <= 'z') {
					playerNums[p] = ch - 'a';
					pos[ch - 'a'][0] = i;
					pos[ch - 'a'][1] = j;
					p++;
				}
			
				map[i][j] = ch;
			}
		}
		
		for(int i = 0; i < P; i++) {
			tokens = new StringTokenizer(br.readLine());
			char player = tokens.nextToken().charAt(0);
			int dpsPlayer = Integer.parseInt(tokens.nextToken());
			
			dps[player - 'a'] = dpsPlayer;
		}
		
		bossHp = Integer.parseInt(br.readLine());
		
		bfs();
		
		while(true) {
			int cnt = 0;	//	이번 턴에 보스 공격 가능한 플레이어 수

			for(int i = 0; i < P; i++) {
				if(playerDist[i] == 0) {	//	이번 턴에 공격 가능한 플레이어
					cnt++;	//	공격 가능한 플레이어 수 1 증가
					bossHp -= dps[playerNums[i]];	//	보스 체력 감소
				}
				else if(playerDist[i] != INF) //	아직 공격하진 못하지만 언젠간 보스를 공격할 수 있는 플레이어
					playerDist[i]--;	//	보스에게 한 칸 가까이 옴
			}
			
			if(bossHp <= 0) {	//	이번 턴에 보스가 죽었을 경우, 이번 턴에 공격한 사람은 전리품 획득 가능
				ans = cnt;
				break;
			}
		}
		
		System.out.println(ans);
	}	//	main-end
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean canGo(int y, int x) {
		return (0 <= y && y < M) && (0 <= x && x < N) && (map[y][x] != 'X');
	}
	
	private static void bfs() {
		Queue<int[]> q = new LinkedList<>();
		dist[bossY][bossX] = 0;
		q.offer(new int[] {bossY, bossX, 0});
	
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cdist = cur[2];	//	현재 위치 정보
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int ndist = cdist + 1;
				
				if(canGo(ny, nx) && ndist < dist[ny][nx]) {				
					dist[ny][nx] = ndist;
					q.offer(new int[] {ny, nx, ndist});
				}
			}
		}	//	while-end

		for(int p = 0; p < P; p++) {
			int pNum = playerNums[p];
			int py = pos[pNum][0];
			int px = pos[pNum][1];
			
			playerDist[p] = dist[py][px];
		}
	}	//	bfs-end
}	//	Main-class-end
```