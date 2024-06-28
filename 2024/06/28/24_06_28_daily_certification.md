# 24_06_28_daily_certification

```
[koreii] #180 데일리인증
2024628
네트워크
- Datalink Layer(L2)
- NIC, L2 Switch
- LAN, WAN
- Broadcast, ARP
- Virtualization
알고리즘 & 코딩 테스트 대비
- BFS
```

# Computer Architecture

# Datalink Layer (L2)

# Network Interface Card(NIC)

![nic.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/nic.jpeg)

![nic2.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/nic2.jpeg)

# L2 Switch

## L2 Access Switch

![l2-access-switch.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/l2-access-switch.jpeg)

## L2 Distribution Switch

![l2-distribution_switch.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/l2-distribution_switch.jpeg)

## vLAN

![vlan.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/vlan.jpeg)

# LAN, WAN, Broadcast

## Broadcast

![broadcast.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/broadcast.jpeg)

![broadcast2.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/broadcast2.jpeg)

## Virtualization

![virtualization.jpeg](24_06_28_daily_certification%200c1d9d7e40004fd29e7700ba334fbf19/virtualization.jpeg)

# Problem Solving (Algorithm & SQL)

### BOJ 10711 **모래성**

[](https://www.acmicpc.net/problem/10711)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;

	private static int N;
	private static int M;

	private static char[][] map;
	private static boolean[][] visited; // 큐에 중복해서 들어가지 않도록 체
	private static int[][] dist; // dist[i][j] : map[i][j]가 수일 경우, (i, j)칸이 무너지기 위해 파도가 쳐야 하는 횟수

	// '.'이거나, 무너진 모래성 칸만 큐에 들어감
	private static final Queue<Node> q = new LinkedList<>();

	private static int ans = 0;

	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		N = Integer.parseInt(tokens.nextToken());
		M = Integer.parseInt(tokens.nextToken());

		map = new char[N][M];
		visited = new boolean[N][M];
		dist = new int[N][M];
		for (int i = 0; i < N; i++)
			Arrays.fill(dist[i], NONE);

		for (int i = 0; i < N; i++) {
			map[i] = br.readLine().toCharArray();
			for (int j = 0; j < M; j++) {
				if (map[i][j] == '.') {
					visited[i][j] = true;
					q.offer(new Node(i, j));
				}
			}
		}

		bfs();

		System.out.println(ans);
	} // main-end

	private static void bfs() {
		while (!q.isEmpty()) {
			int size = q.size();
			Queue<int[]> tmp = new LinkedList<>();
			
			for (int s = 0; s < size; s++) {
				Node cur = q.poll();
				int cy = cur.y;
				int cx = cur.x; // 현재 칸

				for (int d = 0; d < 8; d++) {
					int ny = cy + dy[d];
					int nx = cx + dx[d]; // 다음 칸

					// 다음 칸이 범위 밖이거나, '9'일 경우 skip ('9'인 칸은 절대 무너질 수 없음), 또는 이미 처리한 칸일 경우
					if (!isIn(ny, nx) || map[ny][nx] == '9' || visited[ny][nx])
						continue;

					int emptyCnt = 0; // 다음 칸 주변 8개 칸 중 빈 칸 개수
					int latest = 0; // 주변 모래성 칸 중 가장 늦게 무너진 칸 무너진 시간

					for (int dir = 0; dir < 8; dir++) {
						int nny = ny + dy[dir];
						int nnx = nx + dx[dir]; // 다음 칸 주변의 8칸 확인

						if (!isIn(nny, nnx) || map[nny][nnx] == '9') // 주변 칸이 범위 밖이거나 '9'이면 skip
							continue;
						else if (map[nny][nnx] == '.')
							emptyCnt++; // 주변 빈칸 1개 증가
						else { // 주변 칸이 모래성 칸일 경우
							if (dist[nny][nnx] != NONE) { // 무너졌으면
								latest = Math.max(latest, dist[nny][nnx]); // 가장 마지막에 무너진 모래성 칸 무너진 시간 찾기
								emptyCnt++; // 주변 빈칸 1개 증가
							}
						}
					}

					if (emptyCnt >= map[ny][nx] - '0') { // 현재 모래성 칸도 무너질 수 있으면
						tmp.add(new int[] {ny, nx, latest + 1});
						visited[ny][nx] = true;
						q.offer(new Node(ny, nx));
					}
				}
			}
			
			while(!tmp.isEmpty()) {
				int[] deleted = tmp.poll();
				int delY = deleted[0];
				int delX = deleted[1];
				int delDist = deleted[2];
				dist[delY][delX] = delDist;
				
				ans = Math.max(ans, dist[delY][delX]);
			}
		} // while-end
	} // bfs-end

	private static int[] dy = { 0, 1, 1, 1, 0, -1, -1, -1 };
	private static int[] dx = { 1, 1, 0, -1, -1, -1, 0, 1 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}

	private static class Node {
		int y;
		int x;

		public Node(int y, int x) {
			this.y = y;
			this.x = x;
		}

		@Override
		public String toString() {
			return "[" + this.y + ", " + this.x + "]";
		}
	}
} // Main-class-end
```