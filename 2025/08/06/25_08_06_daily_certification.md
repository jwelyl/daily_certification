# 25_08_06_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree** 개구리의 여행

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/frog-journey/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;			//	맵 크기 N * N
    private static char[][] map;	//	맵
    private static int Q;			//	질의 개수
    
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	map = new char[N][N];
    	
    	for(int y = 0; y < N; y++)
    		map[y] = br.readLine().toCharArray();
    	
    	Q = Integer.parseInt(br.readLine());
    	
    	for(int q = 0; q < Q; q++) {
    		st = new StringTokenizer(br.readLine());
    		int sy = Integer.parseInt(st.nextToken()) - 1;
    		int sx = Integer.parseInt(st.nextToken()) - 1;
    		int ey = Integer.parseInt(st.nextToken()) - 1;
    		int ex = Integer.parseInt(st.nextToken()) - 1;
    		
    		sb.append(dijkstra(sy, sx, ey, ex)).append("\n");
    	}
    	
    	System.out.print(sb);
    }    //    main-end
    
    //	(sy, sx)에서 (ey, ex)까지 도달하는 최소 시간
    private static int dijkstra(int sy, int sx, int ey, int ex) {
    	PriorityQueue<Node> pq = new PriorityQueue<>();
    	int res = INF;

    	//	dist[j][y][x] : (y, x)에 점프력 j로 도달할 수 있는 최소 시간
    	int[][][] dist = new int[6][N][N];

    	for(int j = 0; j <= 5; j++) {
    		for(int y = 0; y < N; y++)
    			Arrays.fill(dist[j][y], INF);
    	}
    	
    	dist[1][sy][sx] = 0;
    	pq.offer(new Node(sy, sx, 1, dist[1][sy][sx]));
    	
    	while(!pq.isEmpty()) {
    		Node cur = pq.poll();
    		int cy = cur.y;
    		int cx = cur.x;	//	현재 위치
    		int cj = cur.j;	//	현재 점프력
    		int cc = cur.d;	//	현재 위치까지 오는데 최단시간
    		
    		if(dist[cj][cy][cx] > cc)	//	더 나아질게 없을 경우 skip
    			continue;
    		
    		//	1. 현재 점프력으로 점프하기
    		for(int d = 0; d < 4; d++) {
    			int ny = cy;
    			int nx = cx;
    			
    			boolean ok = true;	//	d 방향으로 점프 가능하면 true
    			
    			//	이동 경로 확인
    			for(int k = 1; k < cj; k++) {
    				ny = cy + dy[d] * k;
    				nx = cx + dx[d] * k;
    				
    				//	이동 중에 범위를 벗어나거나, 천적이 존재할 경우
    				if(!isIn(ny, nx) || map[ny][nx] == '#') {
    					ok = false;
    					break;
    				}
    			}
    			
    			if(!ok)
    				continue;
    			
    			ny = cy + dy[d] * cj;
    			nx = cx + dx[d] * cj;
    			
    			if(!isIn(ny, nx) || map[ny][nx] != '.')	//	도착칸이 범위 밖이거나 천적이 존재할 경우
    				continue;
    			
    			if(cc + 1 < dist[cj][ny][nx]) {
    				dist[cj][ny][nx] = cc + 1;
    				pq.offer(new Node(ny, nx, cj, dist[cj][ny][nx]));
    			}	
    		}
    		
    		//	2. 점프력 1 증가시키기
    		if(cj < 5) {	//	점프력 증가시킬 수 있을 경우
    			if(cc + (cj + 1) * (cj + 1) < dist[cj + 1][cy][cx]) {
    				dist[cj + 1][cy][cx] = cc + (cj + 1) * (cj + 1);
    				pq.offer(new Node(cy, cx, cj + 1, cc + (cj + 1) * (cj + 1)));
    			}
    		}
    		
    		//	3. 점프력 감소시키기
    		for(int nj = 1; nj < cj; nj++) {
    			if(cc + 1 < dist[nj][cy][cx]) {
    				dist[nj][cy][cx] = cc + 1;
    				pq.offer(new Node(cy, cx, nj, cc + 1));
    			}
    		}
    	}
    	
    	for(int j = 1; j <= 5; j++)
    		res = Math.min(res, dist[j][ey][ex]);

    	return res == INF ? -1 : res;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
    	return  (0 <= y && y < N) && (0 <= x && x < N);
    }
    
    private static class Node implements Comparable<Node> {
    	public int y;
    	public int x;
    	public int j;
    	public int d;
    	
    	public Node(int y, int x, int j, int d) {
    		this.y = y;
    		this.x = x;
    		this.j = j;
    		this.d = d;
    	}
    	
    	@Override
    	public int compareTo(Node other) {
    		return Integer.compare(this.d, other.d);
    	}
    }
}    //    Main-class-end
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