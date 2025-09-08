# 25_09_08_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 메두사와 전사들**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/medusa-and-warriors/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	static int ROAD = 0; // 도로
	static int INF = Integer.MAX_VALUE;

	static char MEDUSA = 'M'; // 메두사 시야에 닿음
	static char BLOCKED = 'B'; // 메두사 시야에 닿은 곳 중 전사들로 인해 가로막힘

	static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	static StringBuilder sb = new StringBuilder();
	static StringTokenizer st;

	static int N; // 마을 크기
	static int M; // 전사 수

	static int sy;
	static int sx; // 메두사 위치 (초기에는 메두사 집 좌표)
	static int EY;
	static int EX; // 공원 좌표 (고정)

	static int[][] map; // 도로 정보
	static int[][] parkDist; // parkDist[y][x] : 공원으로부터 (y, x)까지의 최단거리

	// wArr[nth] : nth번째 전사
	static Warrior[] wArr;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		st = new StringTokenizer(br.readLine());
		sy = Integer.parseInt(st.nextToken());
		sx = Integer.parseInt(st.nextToken());
		EY = Integer.parseInt(st.nextToken());
		EX = Integer.parseInt(st.nextToken());

		map = new int[N][N];
		parkDist = new int[N][N];
		wArr = new Warrior[M + 1];

		st = new StringTokenizer(br.readLine());
		for (int nth = 1; nth <= M; nth++) {
			int y = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			wArr[nth] = new Warrior(y, x);
		}

		for (int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 0; x < N; x++) {
				map[y][x] = Integer.parseInt(st.nextToken());
				parkDist[y][x] = INF;
			}
		}

		setParkDist(); // 공원으로부터 마을 곳곳까지의 최단거리 구하기

		if (parkDist[sy][sx] == INF) { // 메두사가 공원에 도착하지 못할 경우
			System.out.println(-1);
			return;
		}

		while (true) {
			moveMedusa(); // 메두사가 움직임
			if (sy == EY && sx == EX) { // 메두사가 공원에 도착함
				sb.append(0);
				break;
			}

			char[][] stunMap = new char[N][N]; // 메두사가 전사들을 바라봤을때 맵 상태
			int cnt2 = stun(stunMap); // 메두사가 전사들을 바라봄, 돌이 된 전사 수
			int[] cnt13 = moveWarriors(stunMap); // 전사들이 움직임

			int cnt1 = cnt13[0]; // 전사들이 이동한 거리 합
			int cnt3 = cnt13[1]; // 메두사를 공격한 전사 수

			sb.append(cnt1).append(" ").append(cnt2).append(" ").append(cnt3).append("\n");
		}

		System.out.println(sb);
	} // main-end

	static int[] moveWarriors(char[][] stunMap) {
		int[] res = new int[2];

		int[][] medusaDist = new int[N][N];
		for (int y = 0; y < N; y++)
			Arrays.fill(medusaDist[y], INF);
		setMedusaDist(medusaDist); // 현재 시점에선 메두사 위치 고정됨, 메두사 위치로부터 최단거리 구하기

		for (int nth = 1; nth <= M; nth++) {
			if (wArr[nth] == null)
				continue;

			if (!wArr[nth].stunned) {
				if (moveWarrior(nth, medusaDist, stunMap, 1)) { // 첫 번째 이동 가능하면
					res[0]++;
					if (wArr[nth].y == sy && wArr[nth].x == sx) { // 메두사와 같은 칸이 된 경우
						wArr[nth] = null; // 메두사에게 죽음
						res[1]++;
						continue;
					}

					if (moveWarrior(nth, medusaDist, stunMap, 2)) { // 두 번째 이동이 가능하면
						res[0]++;
						if (wArr[nth].y == sy && wArr[nth].x == sx) { // 메두사와 같은 칸이 된 경우
							wArr[nth] = null; // 메두사에게 죽음
							res[1]++;
						}
					}
				}
			} else if (wArr[nth].stunned) // 메두사에 의해 돌이 된 경우
				wArr[nth].stunned = false; // 이동은 못하지만 대신 스턴이 풀림
		}

		return res;
	}

	// nth번째 전사가 cnt번째 이동함
	static boolean moveWarrior(int nth, int[][] medusaDist, char[][] stunMap, int cnt) {
		int start = cnt == 1 ? 0 : 2;
		int end = cnt == 1 ? 3 : 5;

		int minDist = medusaDist[wArr[nth].y][wArr[nth].x]; // 인접한 칸 중 메두사와 가장 가까운 칸의 거리
		int minY = -1;
		int minX = -1; // 그때 그 인접한 칸

		for (int d = start; d <= end; d++) {
			int ny = wArr[nth].y + dy[d % 4];
			int nx = wArr[nth].x + dx[d % 4];

			// 범위 안이고, 메두사 시야 밖이며 가까워지는 방향이면
			if (isIn(ny, nx) && stunMap[ny][nx] != MEDUSA && medusaDist[ny][nx] < minDist) {
				minDist = medusaDist[ny][nx];
				minY = ny;
				minX = nx;
			}
		}

		if (minY == -1 || minX == -1) // 전사가 이동할 수 없는 경우
			return false;

		wArr[nth].y = minY;
		wArr[nth].x = minX; // 전사가 이동
		return true;
	}

	static void setMedusaDist(int[][] medusaDist) {
		Queue<int[]> queue = new LinkedList<>();
		medusaDist[sy][sx] = 0;
		queue.offer(new int[] { sy, sx, 0 });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];

			for (int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];

				// 전사들은 비도로도 갈 수 있음
				if (isIn(ny, nx) && medusaDist[ny][nx] == INF) {
					medusaDist[ny][nx] = cc + 1;
					queue.offer(new int[] { ny, nx, cc + 1 });
				}
			}
		}
	}

	static int stun(char[][] stunMap) {
		List<Integer>[][] wMap = new ArrayList[N][N]; // wMap[y][x] : (y, x)에 존재하는 전사 번호 리스트
		for (int y = 0; y < N; y++) {
			for (int x = 0; x < N; x++)
				wMap[y][x] = new ArrayList<>();
		}

		// 메두사가 전사들을 최대한 많이 돌로 만들었을때 그때 돌이 된 전사 번호 리스트
		List<Integer> stunnedList = new ArrayList<>();

		for (int nth = 1; nth <= M; nth++) {
			if (wArr[nth] != null) { // 살아있는 전사들 wMap에 배치
				int y = wArr[nth].y;
				int x = wArr[nth].x;
				wMap[y][x].add(nth);
			}
		}

		for (int dir = 0; dir < 4; dir++) {
			char[][] dirStunMap = new char[N][N]; // 메두사가 dir 방향을 바라볼때 돌이 되는 상태
			List<Integer> dirStunnedList = new ArrayList<>(); // 메두사가 dir 방향을 바라볼때 돌이 되는 전사들

			// 메두사가 dir 방향을 쳐다봄, 전사가 없다고 가정할 때 볼수 있는 모든 곳들을 체크함, 그때 그 범위 안에 있는 전사들 위치 큐 반환
			Queue<int[]> wQueue = see(dir, wMap, dirStunMap);
			block(dir, wMap, wQueue, dirStunMap, dirStunnedList);

			if (dirStunnedList.size() > stunnedList.size()) {
				stunnedList = dirStunnedList;

				for (int y = 0; y < N; y++) {
					for (int x = 0; x < N; x++)
						stunMap[y][x] = dirStunMap[y][x];
				}
			}
		}

		for (int nth : stunnedList)
			wArr[nth].stunned = true;

		return stunnedList.size();
	}

	static void block(int dir, List<Integer>[][] wMap, Queue<int[]> queue, char[][] dirStunMap,
			List<Integer> dirStunnedList) {
		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int d1 = cur[2];
			int d2 = cur[3];
			int ny1 = cy + dmy[dir][d1];
			int nx1 = cx + dmx[dir][d1];

			if (isIn(ny1, nx1) && dirStunMap[ny1][nx1] != BLOCKED) {
				dirStunMap[ny1][nx1] = BLOCKED;
				queue.offer(new int[] { ny1, nx1, d1, d2 });
			}

			if (d2 != -1) {
				int ny2 = cy + dmy[dir][d2];
				int nx2 = cx + dmx[dir][d2];

				if (isIn(ny2, nx2) && dirStunMap[ny2][nx2] != BLOCKED) {
					dirStunMap[ny2][nx2] = BLOCKED;
					queue.offer(new int[] { ny2, nx2, d1, d2 });
				}
			}
		}

		for (int y = 0; y < N; y++) {
			for (int x = 0; x < N; x++) {
				if (wMap[y][x].size() > 0 && dirStunMap[y][x] == MEDUSA) {
					for (int nth : wMap[y][x])
						dirStunnedList.add(nth);
				}
			}
		}
	}

	static Queue<int[]> see(int dir, List<Integer>[][] wMap, char[][] dirStunMap) {
		Queue<int[]> queue = new LinkedList<>();
		Queue<int[]> res = new LinkedList<>();

		queue.offer(new int[] { sy, sx });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];

			for (int d = 0; d < 3; d++) {
				int ny = cy + dmy[dir][d];
				int nx = cx + dmx[dir][d];

				if (isIn(ny, nx) && dirStunMap[ny][nx] != MEDUSA) {
					dirStunMap[ny][nx] = MEDUSA;
					queue.offer(new int[] { ny, nx });

					if (wMap[ny][nx].size() > 0) { // (ny, nx)칸에 전사가 존재할 경우
						if (dir == 0 || dir == 1) { // 메두사가 위쪽 또는 아래쪽을 볼 경우
							if (nx < sx)
								res.offer(new int[] { ny, nx, 0, 1 });
							else if (nx == sx)
								res.offer(new int[] { ny, nx, 1, -1 });
							else
								res.offer(new int[] { ny, nx, 1, 2 });
						} else { // 메두사가 왼쪽 또는 오른쪽을 볼 경우
							if (ny < sy)
								res.offer(new int[] { ny, nx, 0, 1 });
							else if (ny == sy)
								res.offer(new int[] { ny, nx, 1, -1 });
							else
								res.offer(new int[] { ny, nx, 1, 2 });
						}
					}
				}
			}
		}

		return res;
	}

	// 메두사가 공원을 향해 최단거리로 이동
	static void moveMedusa() {
		int minDist = INF;
		int minY = -1;
		int minX = -1;

		for (int d = 0; d < 4; d++) {
			int ny = sy + dy[d];
			int nx = sx + dx[d];

			if (isIn(ny, nx) && parkDist[ny][nx] < minDist) {
				minDist = parkDist[ny][nx];
				minY = ny;
				minX = nx;
			}
		}

		sy = minY;
		sx = minX;

		// 새로 이동한 칸에 있는 전사들을 죽임
		catchWarriors();
	}

	static void catchWarriors() {
		for (int nth = 1; nth <= M; nth++) {
			// nth번째 전사가 죽지 않았지만 메두사에게 공격받을 경우
			if (wArr[nth] != null && wArr[nth].y == sy && wArr[nth].x == sx)
				wArr[nth] = null; // nth번째 전사가 죽음
		}
	}

	static void setParkDist() {
		Queue<int[]> queue = new LinkedList<>();
		parkDist[EY][EX] = 0;
		queue.offer(new int[] { EY, EX, 0 });

		while (!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];

			for (int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];

				if (isIn(ny, nx) && map[ny][nx] == ROAD && parkDist[ny][nx] == INF) {
					parkDist[ny][nx] = cc + 1;
					queue.offer(new int[] { ny, nx, cc + 1 });
				}
			}
		}
	}

	// 상하좌우
	static int[] dy = { -1, 1, 0, 0 };
	static int[] dx = { 0, 0, -1, 1 };

	// 메두사가 상하좌우 방향을 바라볼때 시야 방향
	static int[][] dmy = { { -1, -1, -1 }, { 1, 1, 1, }, { -1, 0, 1 }, { -1, 0, 1 } };
	static int[][] dmx = { { -1, 0, 1 }, { -1, 0, 1, }, { -1, -1, -1 }, { 1, 1, 1 } };

	static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}

	static class Warrior {
		int y;
		int x;
		boolean stunned; // 메두사가 봐서 돌이 되면 true

		Warrior(int y, int x) {
			this.y = y;
			this.x = x;
			this.stunned = false;
		}
	}
} // Main-class-end
```

### BOJ 1175 배달

[1175번: 배달](http://boj.ma/1175/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final int NONE = 4;    //    방향이 없음
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static char[][] map;
    
    private static int sy;
    private static int sx;
    private static int cy1 = -1;
    private static int cx1 = -1;
    private static int cy2 = -1;
    private static int cx2 = -1;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        for(int y = 0; y < N; y++) {
            map[y] = br.readLine().toCharArray();
            for(int x = 0; x < M; x++) {
                if(map[y][x] == 'S') {
                    sy = y;
                    sx = x;
                }
                else if(map[y][x] == 'C') {
                    if(cy1 == -1 && cx1 == -1) {
                        cy1 = y;
                        cx1 = x;
                    }
                    else {
                        cy2 = y;
                        cx2 = x;
                    }
                }
            }
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<Node> queue = new LinkedList<>();
        boolean[][][][] visited = new boolean[N][M][5][4];
        
        visited[sy][sx][NONE][0] = true;    //        시작 지점에서는 아무 방향으로나 이동 가능
        queue.offer(new Node(sy, sx, NONE, 0, 0));
        
        while(!queue.isEmpty()) {
            Node cur = queue.poll();
            int cy = cur.y;
            int cx = cur.x;
            int cd = cur.d;
            int cstatus = cur.status;
            int ccost = cur.cost;
            
            for(int nd = 0; nd < 4; nd++) {
                if(nd == cd)    //    연속으로 같은 방향 이동 불가
                    continue;
                
                int ny = cy + dy[nd];
                int nx = cx + dx[nd];
                int nstatus = cstatus;
                
                if(isIn(ny, nx) && map[ny][nx] != '#') {
                    if(map[ny][nx] == 'C') {    //    도착점일 경우
                        if(ny == cy1 && nx == cx1) {    //    첫번째 도착점일 경우
                            if(nstatus == 0 || nstatus == 2)
                                nstatus += 1;
                            
                            if(nstatus == 3)
                                return ccost + 1;
                            
                        }
                        else {    //    두번째 도착점일 경우
                            if(nstatus == 0 || nstatus == 1)
                                nstatus += 2;
                            
                            if(nstatus == 3)
                                return ccost + 1;
                        }
                    }
                    
                    if(!visited[ny][nx][nd][nstatus]) {
                        visited[ny][nx][nd][nstatus] = true;
                        queue.offer(new Node(ny, nx, nd, nstatus, ccost + 1));
                    }
                }
            }
        }
        
        return -1;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
    
    private static class Node {
        public int y;
        public int x;
        public int d;
        public int status;    //    첫번째 배달 성공 시 1, 두번째 배달 성공 시 2, 둘 다 배달 시 3, 둘 다 배달 안했을 시 0
        public int cost;
        
        public Node(int y, int x, int d, int status, int cost) {
            this.y = y;
            this.x = x;
            this.d = d;
            this.status = status;
            this.cost = cost;
        }
    }
}    //    Main-class-end
```

### 대장균들의 자식의 수 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/299305)

```sql
-- 코드를 작성해주세요
select t1.id, count(t2.id) as 'child_count' 
from ecoli_data t1 left outer join ecoli_data t2
on t1.id = t2.parent_id
group by t1.id
order by t1.id;
```

### 특정 조건을 만족하는 물고기별 수와 최대 길이 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/298519)

```sql
select count(t1.id) as fish_count, max(t1.length) as max_length, t1.fish_type as fish_type
from fish_info t1 inner join
(
    select fish_type, avg(
        case
            when length is null then 10
            else length
        end
    ) as avg_length
    from fish_info
    group by fish_type
    having avg_length >= 33
) t2
on t1.fish_type = t2.fish_type
group by t1.fish_type
order by t1.fish_type;
```