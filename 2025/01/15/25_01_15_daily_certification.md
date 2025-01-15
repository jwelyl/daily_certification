# 25_01_15_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 16235 나무 재테크

[16235번: 나무 재테크](https://boj.ma/16235/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Deque;
import java.util.LinkedList;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K;
	
	private static int[][] map;	//	map[y][x] : (y, x)에 존재하는 양분
	private static int[][] add;	//	add[y][x] : 겨울에 (y, x)에 추가될 양분
	
	private static final Deque<int[]> aliveTrees = new LinkedList<>();	//	살아있는 나무 정보 {y좌표, x좌표, 나이}
	private static final Deque<int[]> deadTrees = new LinkedList<>();	//	죽은 나무 정보 {y좌표, x좌표, 나이}
	private static final Deque<int[]> newTrees = new LinkedList<>();	//	추가될 나무 정보 {y좌표, x좌표, 나이}
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		map = new int[N][N];
		add = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				map[y][x] = 5;
				add[y][x] = Integer.parseInt(st.nextToken());
			}
		}
		
		//	최초에 주어진 나무 정보, 최초의 M개의 나무는 위치가 서로 다름
		for(int t = 0; t < M; t++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			int k = Integer.parseInt(st.nextToken());
			
			aliveTrees.offerFirst(new int[] {y, x, k});	//	같은 좌표의 나무들 중에서는 어린 나무를 앞에 오도록 놓음
		}
		
		for(int k = 0; k < K; k++) {
			spring();
			summer();
			autumn();
			winter();
		}
		
		System.out.println(aliveTrees.size());
	}	//	main-end
	
	private static void spring() {
		int size = aliveTrees.size();
		
		while(size-- > 0) {
			int[] tree = aliveTrees.pollFirst();	//	같은 칸에 대해 나이 어린 나무부터
			int y = tree[0];
			int x = tree[1];
			int age = tree[2];
			
			if(age > map[y][x])	//	양분이 부족해서 먹을 수 없을 경우
				deadTrees.add(tree);	//	해당 나무는 죽음
			else {
				map[y][x] -= age;	//	해당 칸의 양분을 나이만큼 소모	
				tree[2]++;			//	나이 1 증가
				aliveTrees.offerLast(tree);
			}
		}
	}
	
	private static void summer() {
		while(!deadTrees.isEmpty()) {
			int[] tree = deadTrees.pollFirst();
			int y = tree[0];
			int x = tree[1];
			int age = tree[2];
			
			map[y][x] += age / 2;
		}
	}
	
	private static void autumn() {
		int size = aliveTrees.size();
		
		while(size-- > 0) {
			int[] tree = aliveTrees.pollFirst();	//	살아있는 나무들에 대해
			int y = tree[0];
			int x = tree[1];
			int age = tree[2];
			
			if(age % 5 == 0) {	//	나이가 5의 배수일 경우, 번식
				for(int d = 0; d < 8; d++) {
					if(isIn(y + dy[d], x + dx[d]))	//	인접한 8방향 중 범위 안인 칸에 나이 1인 나무 추가
						newTrees.offer(new int[] {y + dy[d], x + dx[d], 1});
				}
			}
			
			aliveTrees.offerLast(tree);
		}
		
		while(!newTrees.isEmpty())
			aliveTrees.offerFirst(newTrees.poll());
	}
	
	private static void winter() {
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				map[y][x] += add[y][x];	//	양분 추가
		}
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```

### **BOJ 2325 개코전쟁**

[2325번: 개코전쟁](https://boj.ma/2325/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 1_000 * 1_000 * 1_000 + 1;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    //	graph[v] : v에 연결된 간선 번호
    private static List<Integer>[] graph;
    private static Edge[] edges;
    
    //	shortestPath[v] : 1에서 N까지의 최단거리에서 N부터 1로 거슬러 올라갈때 v에서 1로 가기 위해 연결되어 있는 간선 번호
    private static int[] shortestPath;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
    	N = Integer.parseInt(st.nextToken());
    	M = Integer.parseInt(st.nextToken());
    	
    	graph = new ArrayList[N + 1];
    	for(int v = 0; v <= N; v++)
    		graph[v] = new ArrayList<>();
    	edges = new Edge[M + 1];
    	shortestPath = new int[N + 1];
    	
    	for(int e = 1; e <= M; e++) {
    		st = new StringTokenizer(br.readLine());
        	int v1 = Integer.parseInt(st.nextToken());
        	int v2 = Integer.parseInt(st.nextToken());
        	int c = Integer.parseInt(st.nextToken());
        	
        	graph[v1].add(e);
        	graph[v2].add(e);
        	edges[e] = new Edge(v1, v2, c);
    	}
    	
    	dijkstra(false);
    	
    	System.out.println(res());
    }    //    main-end
    
    private static int res() {
    	int res = 0;
    	
    	int cur = N;
    	
    	while(cur != 1) {
    		int e = shortestPath[cur];
    		Edge edge = edges[e];
    		int v1 = edge.v1;
    		int v2 = edge.v2;
    		int next = cur == v1 ? v2 : v1;
    		
    		edge.destroyed = true;	//	edge를 파괴
    		res = Math.max(res, dijkstra(true));	//	edge를 파괴했을때의 최단경로와 비교
    		edge.destroyed = false;
    	
    		cur = next;
    	}
    	
    	return res;
    }
    
    private static int dijkstra(boolean destroy) {
    	PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[1], n2[1]));
    	int[] dist = new int[N + 1];
    	Arrays.fill(dist, INF);
    	
    	dist[1] = 0;
    	pq.offer(new int[] {1, dist[1]});
    	
    	while(!pq.isEmpty()) {
    		int[] cur = pq.poll();
    		int cv = cur[0];
    		int cc = cur[1];
    		
    		if(cv == N) {
    			dist[N] = cc;
    			break;
    		}
    		
    		if(dist[cv] < cc)
    			continue;
    		
    		for(int e : graph[cv]) {
    			Edge edge = edges[e];
    			int v1 = edge.v1;
    			int v2 = edge.v2;
    			int cost = edge.cost;
    			
    			//	1개 도로 파괴하는 상황에서 해당 도로가 파괴된 경우 skip
    			if(destroy && edge.destroyed)
    				continue;
    			
    			int nv = v1 == cv ? v2 : v1;
    			int nc = cc + cost;
    			
    			if(nc < dist[nv]) {
    				dist[nv] = nc;
    				if(!destroy)
    					shortestPath[nv] = e;
    				
    				pq.offer(new int[] {nv, dist[nv]});
    			}
    		}
    	}
    	
    	return dist[N];
    }
    
    private static class Edge {
    	public int v1;
    	public int v2;
    	public int cost;
    	private boolean destroyed;
    	
    	public Edge(int v1, int v2, int cost) {
    		this.v1 = v1;
    		this.v2 = v2;
    		this.cost = cost;
    		this.destroyed = false;
    	}
    }
}    //    Main-class-end
```