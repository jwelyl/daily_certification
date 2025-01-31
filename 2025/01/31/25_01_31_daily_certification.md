# 25_01_31_daily_certification

```
[#031 koreii] 데일리인증 20250131
1. 프로젝트 리팩토링
- DTO, Repository 설계
2. 코딩 테스트 대비 복습문제 풀이 
- BFS (16933 벽 부수고 이동하기 3)
3. 코딩 테스트 대비 알고리즘 학습 
- Disjoint Set, MST, DFS, Forest, LCA (BOJ 1396 크루스칼의 공) 
4. 정보처리기사 필기
- 기출 1회분
- 요약본 스캔
```

# Problem Solving (Algorithm & SQL)

### **BOJ 16933 벽 부수고 이동하기 3**

[16933번: 벽 부수고 이동하기 3](http://boj.ma/16933/t)

**visited 배열에 최단경로 길이를 저장하는 경우 (비효율적)**

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
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        if(N == 1 && M == 1)
            return 1;
        
        Queue<int[]> queue = new LinkedList<>();
        int[][][] visited = new int[N][M][K + 1];    //    visited[y][x][k] : (y, x)에 벽을 k개 부수고 방문했을때 이동 횟수
        int res = INF;
        
        for(int y = 0; y < N; y++) {
        	for(int x = 0; x < M; x++)
        		Arrays.fill(visited[y][x], INF);
        }

        visited[0][0][0] = 1;
        queue.offer(new int[] {0, 0, 0, 1});    //    (0, 0)에 벽을 0개 부순 상태로 1칸째임
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];        //    현재 위치 (cy, cx)
            int cw = cur[2];        //    (cy, cx)로 올 때까지 벽을 부순 횟수
            int ccnt = cur[3];      //    (cy, cx)까지 이동한 횟수 (홀수면 낮, 짝수면 밤)
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    다음에 갈 위치
                int nw = cw;            //    (ny, nx)에 갈 때까지 벽을 부순 횟수
                
                if(!isIn(ny, nx))    //    범위를 벗어난 경우
                    continue;
                
                if(map[ny][nx] == '0') {    //    다음 칸이 벽이 아닐 경우
                    if(visited[ny][nx][cw] > ccnt + 1) {    //    현재 벽 부순 횟수로 방문한 적 없을 경우
                        visited[ny][nx][cw] = ccnt + 1;
                        queue.offer(new int[] {ny, nx, cw, ccnt + 1});
                    }
                }
                else {    //    다음 칸이 벽일 경우
                    if(cw < K) {    //    아직 벽을 더 부술 수 있을 경우
                        nw = cw + 1;
                        
                        if(ccnt % 2 == 1) {	//	현재 낮이라서 벽을 바로 부술 수 있을 경우
                        	if(visited[ny][nx][nw] > ccnt + 1) {
                        		visited[ny][nx][nw] = ccnt + 1;
                        		queue.offer(new int[] {ny, nx, nw, ccnt + 1});
                        	}
                        }
                        else {	//	현재 밤이라서 하루 더 기다렸다가 벽을 부숴야 할 경우
                        	if(visited[ny][nx][nw] > ccnt + 2) {
                        		visited[ny][nx][nw] = ccnt + 2;
                        		queue.offer(new int[] {ny, nx, nw, ccnt + 2});
                        	}
                        }
                    }
                }
            }
        }
        
        for(int k = 0; k <= K; k++) {
        	if(res > visited[N - 1][M - 1][k])
        		res = visited[N - 1][M - 1][k];
        }
        
        return res == INF ? -1 : res;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

**BFS의 특징을 응용하는 경우**

- **틀린 코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    private static boolean[][][] visited;    //    visited[y][x][k] : (y, x)에 벽을 k개 부수고 방문했으면 true
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        visited = new boolean[N][M][K + 1];
        
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        if(N == 1 && M == 1)
            return 1;
        
        Queue<int[]> queue = new LinkedList<>();
        visited[0][0][0] = true;
        queue.offer(new int[] {0, 0, 0, 1});    //    (0, 0)에 벽을 0개 부순 상태로 1칸째임
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];        //    현재 위치 (cy, cx)
            int cw = cur[2];        //    (cy, cx)로 올 때까지 벽을 부순 횟수
            int ccnt = cur[3];      //    (cy, cx)까지 이동한 횟수 (홀수면 낮, 짝수면 밤)
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    다음에 갈 위치
                int nw = cw;            //    (ny, nx)에 갈 때까지 벽을 부순 횟수
                int ncnt = ccnt + 1;    //    (ny, nx)까지 이동한 횟수
                
                if(!isIn(ny, nx))    //    범위를 벗어난 경우
                    continue;
                
                if(ny == N - 1 && nx == M - 1)    //    다음 칸이 도착칸일 경우
                    return ncnt;    //    도착칸은 벽이 아니므로 무조건 갈 수 있음
                
                if(map[ny][nx] == '0') {    //    다음 칸이 벽이 아닐 경우
                    if(!visited[ny][nx][cw]) {    //    현재 벽 부순 횟수로 방문한 적 없을 경우
                        visited[ny][nx][cw] = true;
                        queue.offer(new int[] {ny, nx, cw, ncnt});
                    }
                }
                else {    //    다음 칸이 벽일 경우
                    if(cw < K) {    //    아직 벽을 더 부술 수 있을 경우
                        nw = cw + 1;
                        
                        if(!visited[ny][nx][nw]) {    //    아직 nw개의 벽을 부순 채 방문한 적이 없을 경우
                            visited[ny][nx][nw] = true;
                            //    현재 낮일 경우(ccnt % 2 = 1) 바로 벽 부숴서 이동 가능, 밤일 경우 하루 더 기다려서 이동 가능
                            queue.offer(new int[] {ny, nx, nw, ccnt % 2 == 1 ? ncnt : ccnt + 2});
                        }
                    }
                }
            }
        }
        
        return -1;    //    (N - 1, M - 1)에 도착하지 못한 경우   
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

BFS의 성질로 문제를 해결하려면 queue에 한번에 들어가는 노드들은 경로 길이가 같은 노드들이어야 한다. 밤일 경우 기다린 시간을 반영해서 경로 길이를 +2한 다음에 queue에 넣으면 BFS의 성질이 깨지게 된다.

- **정답 코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    private static boolean[][][] visited;    //    visited[y][x][k] : (y, x)에 벽을 k개 부수고 방문했으면 true
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        visited = new boolean[N][M][K + 1];
        
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        if(N == 1 && M == 1)
            return 1;
        
        Queue<int[]> queue = new LinkedList<>();
        visited[0][0][0] = true;
        queue.offer(new int[] {0, 0, 0, 1});    //    (0, 0)에 벽을 0개 부순 상태로 1칸째임
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];        //    현재 위치 (cy, cx)
            int cw = cur[2];        //    (cy, cx)로 올 때까지 벽을 부순 횟수
            int ccnt = cur[3];      //    (cy, cx)까지 이동한 횟수 (홀수면 낮, 짝수면 밤)
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    다음에 갈 위치
                int nw = cw;            //    (ny, nx)에 갈 때까지 벽을 부순 횟수
                int ncnt = ccnt + 1;    //    (ny, nx)까지 이동한 횟수
                
                if(!isIn(ny, nx))    //    범위를 벗어난 경우
                    continue;
                
                if(ny == N - 1 && nx == M - 1)    //    다음 칸이 도착칸일 경우
                    return ncnt;    //    도착칸은 벽이 아니므로 무조건 갈 수 있음
                
                if(map[ny][nx] == '0') {    //    다음 칸이 벽이 아닐 경우
                    if(!visited[ny][nx][cw]) {    //    현재 벽 부순 횟수로 방문한 적 없을 경우
                        visited[ny][nx][cw] = true;
                        queue.offer(new int[] {ny, nx, cw, ncnt});
                    }
                }
                else {    //    다음 칸이 벽일 경우
                    if(cw < K) {    //    아직 벽을 더 부술 수 있을 경우
                        nw = cw + 1;
                        
                        if(!visited[ny][nx][nw]) {    //    아직 nw개의 벽을 부순 채 방문한 적이 없을 경우
                            if(ccnt % 2 == 1) {	//	현재 낮이라서 바로 (ny, nx)로 벽을 부수고 갈 수 있을 경우
                            	visited[ny][nx][nw] = true;
                            	queue.offer(new int[] {ny, nx, nw, ncnt});
                            }
                            else	//	현재 밤이라서 바로 (ny, nx)로 갈 수 없을 경우, 일단 현재 칸에서 대기
                            	queue.offer(new int[] {cy, cx, cw, ncnt});
                        }
                    }
                }
            }
        }
        
        return -1;    //    (N - 1, M - 1)에 도착하지 못한 경우   
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

벽을 부숴야 할 때 낮일 경우에는 바로 경로 + 1해서 (ny, nx)를 queue에 넣고, 밤일 경우에는 경로 + 1해서 현재 칸 (cy, cx)를 queue에 넣는다.

### **BOJ 1396 크루스칼의 공**

[1396번: 크루스칼의 공](http://boj.ma/1396/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_A = 20;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	
	private static final List<int[]> edges = new ArrayList<>();	//	간선
	
	private static int[] parents;
	
	private static List<Integer>[] graph;
	private static int[] costs;
	private static int[] depths;
	private static int[] subtrees;
	
	private static int vNum = 0;
	private static int[][] ancestors;	//	ancestors[i][j] : i 정점의 2^j번째 조상
	
	private static final Set<Integer> rootSet = new HashSet<>();
	
	private static int Q;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		parents = new int[2 * N + 2];
		vNum = N;
		
		graph = new ArrayList[2 * N + 2];
		costs = new int[2 * N + 2];
		depths = new int[2 * N + 2];
		subtrees = new int[2 * N + 2];
		
		ancestors = new int[2 * N + 2][MAX_A];
		
		for(int v = 0; v < 2 * N + 2; v++) {
			graph[v] = new ArrayList<>();
			parents[v] = v;
		}
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			edges.add(new int[] {Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())});
		}
		
		Collections.sort(edges, (e1, e2) -> Integer.compare(e1[2], e2[2]));
		
		for(int[] edge : edges) {
			int v1 = edge[0];
			int v2 = edge[1];
			int c = edge[2];
			
			int v1Ds = find(v1);
			int v2Ds = find(v2);
			
			if(v1Ds != v2Ds)
				union(v1Ds, v2Ds, c);
		}
		
		for(int v = 1; v <= vNum; v++) {
			int root = find(v);
			
			if(root != 0)
				rootSet.add(root);
		}
		
		for(int root : rootSet)
			dfs(root, 0, 1);
		
		makeAncestorsTable();
		
		Q = Integer.parseInt(br.readLine());
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			if(find(v1) != find(v2)) {	//	v1, v2가 같은 component가 아닐 경우
				sb.append("-1\n");
				continue;
			}
			
			int lca = lca(v1, v2);
			
			sb.append(costs[lca]).append(" ").append(subtrees[lca]).append("\n");
		}
		
		System.out.print(sb);
	} //    main-end
	
	private static void union(int v1Ds, int v2Ds, int cost) {
		vNum++;
		graph[v1Ds].add(vNum);
		graph[v2Ds].add(vNum);
		graph[vNum].add(v1Ds);
		graph[vNum].add(v2Ds);
		
		parents[v1Ds] = vNum;
		parents[v2Ds] = vNum;
		costs[vNum] = cost;
	}
	
	private static int find(int v) {
		if(v == parents[v])
			return v;
		
		return parents[v] = find(parents[v]);
	}
	
	//	vertex의 깊이는 depth, 바로 윗 조상(2^0번째 조상)은 parent
	private static void dfs(int vertex, int parent, int depth) {
		depths[vertex] = depth;
		ancestors[vertex][0] = parent;
		subtrees[vertex] = vertex <= N ? 1 : 0;
		
		for(int next : graph[vertex]) {
			if(depths[next] == 0) {	//	아직 방문하지 않은 자식인 경우
				dfs(next, vertex, depth + 1);
				subtrees[vertex] += subtrees[next];
			}
		}
	}
	
	private static void makeAncestorsTable() {
		for(int i = 1; i < MAX_A; i++) {
			for(int j = 1; j < 2 * N + 2; j++) {
				int ancestor = ancestors[j][i - 1];	//	j의 2^(i-1)번째 조상
				
				//	j의 2^i번째 조상은 ancestor의 2^(i-1)번째 조상
				ancestors[j][i] = ancestors[ancestor][i - 1];
			}
		}
	}
	
	private static int lca(int v1, int v2) {
		int x;	//	v1 중 깊이가 더 얕은 정점
		int y;	//	v2 중 깊이가 더 얕은 정점
		int diff;	//	x, y의 깊이 차이
		int exp = 0;
		
		if(depths[v1] <= depths[v2]) {
			x = v1;
			y = v2;
		}
		else {
			x = v2;
			y = v1;
		}
		diff = depths[y] - depths[x];

		while(diff > 0) {
			int r = diff % 2;	//	2로 나눈 나머지
			diff /= 2;			//	2로 나눔
			
			if(r == 1)	//	2^exp만큼 위로 올라감
				y = ancestors[y][exp];
			
			exp++;
		}

		if(x == y)	//	깊이가 같은 두 정점이 같으면 그게 공통조상임
			return x;
		else {
			for(int i = MAX_A - 1; i >= 0; i--) {
				int xx = ancestors[x][i];
				int yy = ancestors[y][i];	//	x, y의 2^i번째 조상

				if(xx != yy) {	//	둘의 2^i번째 조상이 다를 경우, 2^i번째 조상까지 올라간 후 더 올라가봐야 함
					x = xx;
					y = yy;
				}
			}
		
			return ancestors[x][0];
		}
	}
} //    Main-class-end
```