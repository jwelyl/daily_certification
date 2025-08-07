# 25_08_07_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 미생물 연구**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/microbial-research/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	배양 용기 크기 N * N
	private static int Q;	//	투입할 미생물 수
	
	private static int[][] map;

	private static Germ[] aliveGermArr;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		aliveGermArr = new Germ[Q + 1];
		
		for(int q = 1; q <= Q; q++) {
			st = new StringTokenizer(br.readLine());
			int x1 = Integer.parseInt(st.nextToken());
			int y1 = Integer.parseInt(st.nextToken());
			int x2 = Integer.parseInt(st.nextToken());
			int y2 = Integer.parseInt(st.nextToken());			
		
			insert(q, y1, x1, y2, x2);
			move();
			sb.append(result()).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	//	미생물을 투입하기
	public static void insert(int num, int y1, int x1, int y2, int x2) {
		for(int y = y1; y < y2; y++) {
			for(int x = x1; x < x2; x++)
				map[y][x] = num;
		}
	}
	
	//	미생물 전부 새로운 용기에 이동시키기
	public static void move() {
		int[] cnts = new int[Q + 1];	//	cnts[i] : 현재 용기에 남아있는 i번째 미생물의 수, 2개 이상이면 분리되었다는 뜻
		int[][] tmp = new int[N][N];	//	옮겨질 용기
		
		boolean[][] visited = new boolean[N][N];
		List<Germ> germList = new ArrayList<>();
		PriorityQueue<Germ> pq = new PriorityQueue<>();
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++) {
				if(map[y][x] != 0 && !visited[y][x]) {
					cnts[map[y][x]]++;
					germList.add(bfs(y, x, map[y][x], visited));
				}
			}
		}
		
		for(Germ germ : germList) {
			if(cnts[germ.num] == 1)		//	2개 이상으로 나누어지지 않은 미생물만 새로운 용기에 이동시키기 
				pq.offer(germ);
		}
		
		while(!pq.isEmpty()) {
			Germ germ = pq.poll();
			
			OUTER:
			for(int x = 0; x < N; x++) {
				for(int y = 0; y < N; y++) {
					if(canPut(y, x, germ, tmp)) {	//	germ을 새 용기의 (y, x)에 옮길 수 있을 경우
						put(y, x, germ, tmp);		//	germ을 새 용기의 (y, x)에 옮김
						aliveGermArr[germ.num] = germ;
						break OUTER;
					}
				}
			}
		}
		
		map = tmp;
	}
	
	private static int result() {
		int res = 0;
		boolean[][] visited = new boolean[Q + 1][Q + 1];
		
		for(int cy = 0; cy < N; cy++) {
			for(int cx = 0; cx < N; cx++) {
				if(map[cy][cx] != 0) {	//	미생물이 있을 경우
					int cnum = map[cy][cx];
					int csize = aliveGermArr[cnum].posList.size();
					
					for(int d = 0; d < 4; d++) {
						int ny = cy + dy[d];
						int nx = cx + dx[d];
						
						if(isIn(ny, nx) && map[ny][nx] != 0 && map[ny][nx] != cnum) {
							int nnum = map[ny][nx];
							int nsize = aliveGermArr[nnum].posList.size();
							
							if(!visited[cnum][nnum] && !visited[nnum][cnum]) {
								visited[cnum][nnum] = true;
								visited[nnum][cnum] = true;
								
								res += csize * nsize;
							}
						}
					}
				}
			}
		}
		
		Arrays.fill(aliveGermArr, null);
		
		return res;
	}
	
	private static boolean canPut(int y, int x, Germ germ, int[][] nmap) {
		Pos pos = germ.posList.get(0);	//	germ이 차지하는 좌표 중 가장 x좌표가 작은 좌표, x좌표가 같다면 y좌표가 가장 작은 좌표
		
		int diffY = y - pos.y;
		int diffX = x - pos.x;
		
		for(Pos p : germ.posList) {
			if(!isIn(p.y + diffY, p.x + diffX) || nmap[p.y + diffY][p.x + diffX] != 0)
				return false;
		}
		
		return true;
	}
	
	private static void put(int y, int x, Germ germ, int[][] nmap) {
		Pos pos = germ.posList.get(0);	//	germ이 차지하는 좌표 중 가장 x좌표가 작은 좌표, x좌표가 같다면 y좌표가 가장 작은 좌표
		int diffY = y - pos.y;
		int diffX = x - pos.x;
		
		for(Pos p : germ.posList)
			nmap[p.y + diffY][p.x + diffX] = germ.num;
	}
	
	private static Germ bfs(int y, int x, int num, boolean[][] visited) {
		Queue<Pos> queue = new LinkedList<>();
		Germ germ = new Germ(num);
		
		visited[y][x] = true;
		germ.posList.add(new Pos(y, x));
		queue.offer(new Pos(y, x));
		
		while(!queue.isEmpty()) {
			Pos cur = queue.poll();
			int cy = cur.y;
			int cx = cur.x;
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && map[ny][nx] == num && !visited[ny][nx]) {
					visited[ny][nx] = true;
					germ.posList.add(new Pos(ny, nx));
					queue.offer(new Pos(ny, nx));
				}
			}
		}
		
		//	미생물들이 차지하는 칸들을 x좌표가 작은 순으로, x좌표가 같다면 y좌표가 작은 순으로 정렬하기
		Collections.sort(germ.posList);
		
		return germ;
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
	
	private static class Pos implements Comparable<Pos> {
		public int y;
		public int x;
		
		public Pos(int y, int x) {
			this.y = y;
			this.x = x;
		}
		
		@Override
		public int compareTo(Pos other) {
			int res = Integer.compare(this.x, other.x);	//	x 좌표가 작을 수록 우선순위가 높음
		
			if(res == 0)	//	x 좌표가 같을 경우, y 좌표가 작을 수록 우선순위가 높음	
				res = Integer.compare(this.y, other.y);
		
			return res;
		}
	}
	
	private static class Germ implements Comparable<Germ> {
		public int num;	//	미생물 번호
		public final List<Pos> posList = new ArrayList<>();	//	미생물이 차리하고 있는 칸
		
		public Germ(int num) {
			this.num = num;
		}
		
		@Override
		public int compareTo(Germ other) {
			int res = Integer.compare(other.posList.size(), this.posList.size());	//	미생물 크기가 클 수록 우선순위가 높음
		
			if(res == 0)	//	미생물 크기가 같을 경우, 미생물 번호가 작을 수록(즉, 먼저 놓였을 수록) 우선순위가 높음
				res = Integer.compare(this.num, other.num);
		
			return res;
		}
	}
}	//	Main-class-end
```

### BOJ 9328 열쇠

[9328번: 열쇠](http://boj.ma/9328/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.Queue;
import java.util.ArrayList;
import java.util.LinkedList;

public class Main {
    private static final char WALL = '*';
    private static final char DOCS = '$';
    private static final int MAX = 26;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트 케이스 개수
    private static int H;    //    행 크기
    private static int W;    //    열 크기
    
    private static char[][] map;
    private static boolean[][] visited;
    
    //    gatePos[ch - 'A'] : ch에 해당하는 문이 존재하는 좌표 리스트
    private static final List<int[]>[] gatePos = new ArrayList[MAX];
    
    private static char[] keys;    //    초기에 존재하는 열쇠 목록
    
    public static void main(String[] args) throws IOException {
        for(int g = 0; g < MAX; g++)
            gatePos[g] = new ArrayList<>();
        
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            for(int g = 0; g < MAX; g++)
                gatePos[g].clear();
            
            st = new StringTokenizer(br.readLine());
            H = Integer.parseInt(st.nextToken());
            W = Integer.parseInt(st.nextToken());
            
            map = new char[H + 2][W + 2];
            visited = new boolean[H + 2][W + 2];
            
            for(int y = 1; y <= H; y++) {
                String input = br.readLine();
                for(int x = 1; x <= W; x++) {
                    map[y][x] = input.charAt(x - 1);
                    
                    if('A' <= map[y][x] && map[y][x] <= 'Z')    //    문일경우
                        gatePos[map[y][x] - 'A'].add(new int[] {y, x});    //    각 알파벳에 해당하는 문의 좌표 저장
                }
            }
            
            keys = br.readLine().toCharArray();    //    최초 보유하고 있는 열쇠 목록
            
            if(keys[0] != '0') {    //    처음 보유한 열쇠가 있을 경우
                for(char key : keys) {
                    for(int[] pos : gatePos[key - 'a']) {    //    가지고 있는 열쇠에 해당하는 문 좌표 가져오기
                        int y = pos[0];
                        int x = pos[1];
                    
                        map[y][x] = '.';    //    해당 문 미리 제거하기
                    }
                }
            }
            
            sb.append(bfs()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static int bfs() {
        Queue<int[]> queue = new LinkedList<>();
        int res = 0;
        
        visited[0][0] = true;
        queue.offer(new int[] {0, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    다음에 가려는 칸
                
                if(isIn(ny, nx) && !visited[ny][nx] && map[ny][nx] != WALL) {    //    다음에 가려는 칸이 범위 안에 있고, 방문하지 않았으며, 벽이 아닐 경우    
                    if(!('A' <= map[ny][nx] && map[ny][nx] <= 'Z')) {    //    문이 아닐 경우, 이곳에서부터 출발할 수 있으므로 큐에 넣음
                        if(map[ny][nx] == DOCS)    //    문서일 경우
                            res++;
                        else if('a' <= map[ny][nx] && map[ny][nx] <= 'z') {    //    열쇠일 경우
                            for(int[] pos : gatePos[map[ny][nx] - 'a']) {    //    가지고 있는 열쇠에 해당하는 문 좌표 가져오기
                                int y = pos[0];
                                int x = pos[1];
                    
                                map[y][x] = '.';    //    해당 문 제거하기
                                
                                if(visited[y][x])    //    해당 문에 이미 방문한 적이 있을 경우
                                    queue.offer(new int[] {y, x});    //    그 문 위치에서 출발 가능
                            }
                        }
                    
                        queue.offer(new int[] {ny, nx});
                    }
                    //    문일 경우에는, 문까지 도달했던 적이 있음을 표시하기 위해 방문처리만 함
                    //    문이 잠겨있으면 문에서부터 출발은 불가하므로 큐에 넣지는 않음
                    visited[ny][nx] = true;
                }
            }
        }
        
        return res;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y <= H + 1) && (0 <= x && x <= W + 1);
    }
}    //    Main-class-end
```