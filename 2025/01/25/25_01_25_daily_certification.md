# 25_01_25_daily_certification

```
[#025 koreii] 데일리인증 20250125
1. 프로젝트 리팩토링 
- 테스트용 클라이언트 React 구현
- API 테스트
2. 코딩 테스트 대비 알고리즘 학습 
- BFS, Parametric Search (BOJ 5465 곰돌이) 
3. 정보처리기사 필기 (프로그래밍 언어 활용)
- 네트워크 (인터넷, OSI 7 Layers, IP, TCP/UDP)
- IP (IPv4, Header, Class, CIDR, Subnet, IPv6, Routing Protocol)
- TCP, UPD
- 문제풀이
```

# Problem Solving (Algorithm & SQL)

### **BOJ 5465 곰돌이**

[5465번: 곰돌이](https://boj.ma/5465/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 800 * 800 + 1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int S;
	
	private static char[][] map;
	private static int[][] beeTimes;		//	beeTimes[y][x] : (y, x)에 벌이 도착하는 시간
	private static boolean[][] visited;		//	BFS 방문 배열

	private static int sy;
	private static int sx;	//	곰돌이 출발 위치
	
	private static final Queue<int[]> queue = new LinkedList<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		S = Integer.parseInt(st.nextToken());
		
		map = new char[N][N];
		beeTimes = new int[N][N];
		visited = new boolean[N][N];
		
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			Arrays.fill(beeTimes[y], INF);
			for(int x = 0; x < N; x++) {
				if(map[y][x] == 'H') {	//	(y, x)에 벌이 있을 경우
					beeTimes[y][x] = 0;
					visited[y][x] = true;
					queue.offer(new int[] {y, x, beeTimes[y][x]});
				}
				else if(map[y][x] == 'M') {	//	(y, x)에서 곰돌이가 출발할 경우
					sy = y;
					sx = x;
				}
			}
		}
			
		beeBfs();	//	벌들이 퍼짐
		
		System.out.println(parametricSearch());
	} //	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = INF;
		int res = -1;
		
		while(start <= end) {
			int mid = (start + end) / 2;	//	mid만큼 꿀을 먹고 출발한다고 가정
			
			if(bfs(mid)) {	//	mid만큼 꿀 먹고 출발해도 집에 갈수 있을 경우
				res = mid;	//	일단 mid 저장
				start = mid + 1;	//	더 오랜 시간 꿀을 먹을 수 있나 체크
			}
			else	//	mid만큼 꿀 먹고 출발하면 집에 갈수 없으면
				end = mid - 1;	//	더 적은 시간 꿀을 먹어야 함
		}
		
		return res;
	}
	
	private static void clear() {
		for(int y = 0; y < N; y++)
			Arrays.fill(visited[y], false);
		queue.clear();
	}
	
	//	꿀 빠는 시간이 honeyTime일때 집에 도착 가능하면 true
	private static boolean bfs(int honeyTime) {
		if(honeyTime >= beeTimes[sy][sx])	//	곰돌이가 꿀 빠는 동안 벌이 꿀 빠는 칸으로 올 경우
			return false;
		
		clear();
		
		visited[sy][sx] = true;
		queue.offer(new int[] {sy, sx, S, honeyTime});	//	{현재 y좌표, 현재 x좌표, 더 갈수 있는 칸 수, 도착 시각}
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];		//	(cy, cx)에 곰돌이 있음
			int cr = cur[2];		//	최대로 갈 수 있는 S칸 중 더 갈수 있는 남은 칸 개수
			int ctime = cur[3];		//	(cy, cx)에 도달하는 최소 시간
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	가려는 칸
				int nr = cr - 1 == 0 ? S : cr - 1;			//	1분내에 갈 수 있는 칸을 다 갔을 경우 S, 아니면 남은 칸
				int ntime = cr - 1 == 0 ? ctime + 1 : ctime; 	//	1분내에 갈 수 있는 칸을 다 갔을 경우 다음 1분 필요, 아니면 기존 1분 내에서 해결 가능
				
				//	가려는 칸이 범위 안이고 나무가 없으며 방문하지 않았고 벌보다 먼저 도달 가능할 경우
				if(isIn(ny, nx) && map[ny][nx] != 'T' && !visited[ny][nx] && beeTimes[ny][nx] > ntime) {
					if(map[ny][nx] == 'D')	//	도착점일 경우
						return true;
				
					visited[ny][nx] = true;
					queue.offer(new int[] {ny, nx, nr, ntime});
				}
			}
		}
		
		return false;
	}
	
	private static void beeBfs() {
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int ctime = cur[2];	//	(cy, cx)에 벌이 오는 최소 시간
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && map[ny][nx] != 'T' && map[ny][nx] != 'D' && !visited[ny][nx]) {
					beeTimes[ny][nx] = ctime + 1;
					visited[ny][nx] = true;
					queue.offer(new int[] {ny, nx, beeTimes[ny][nx]});
				}
			}
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
} //	Main-class-end
```