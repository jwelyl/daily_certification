# 25_01_14_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 17142 연구소 3

[17142번: 연구소 3](https://boj.ma/17142/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int EMPTY = 0;		//	빈칸
	private static final int WALL = 1;		//	벽
	private static final int VIRUS = 2;		//	바이러스
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
		
	private static int N;	//	연구소 크기 N * N
	private static int M;	//	활성화시킬 바이러스 개수
	
	private static int[][] lab;	//	연구소
	private static final List<int[]> viruses = new ArrayList<>();	//	최초에 주어지는 비활성 바이러스 위치
	
	private static int empties = 0;	//	최초 빈칸 개수
	private static int answer = 0;	//	모든 빈칸을 바이러스로 채우기 위한 최소시간
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		lab = new int[N][N];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++) {
				lab[y][x] = Integer.parseInt(st.nextToken());
				if(lab[y][x] == VIRUS)
					viruses.add(new int[] {y, x});
				else if(lab[y][x] == EMPTY)
					empties++;
			}
		}
		
		if(empties == 0) {	//	처음부터 빈칸 없이 바이러스가 가득 찬 경우
			System.out.println(0);
			return;
		}
		
		answer = N * N + 1;
		
		combination(0, 0, new int[M]);
		
		System.out.println(answer == N * N + 1 ? -1 : answer);
	}	//	main-end
	
	private static void combination(int nth, int start, int[] selected) {
		if(nth == M) {	//	최초에 활성화시킬 M개의 바이러스 선택을 완료한 경우
			Queue<int[]> queue = new LinkedList<>();
			boolean[][] visited = new boolean[N][N];
			
			for(int i : selected) {
				int[] virus = viruses.get(i);
				visited[virus[0]][virus[1]] = true;
				queue.offer(virus);
			}
			
			bfs(queue, visited);
			return;
		}
		
		for(int i = start; i < viruses.size(); i++) {
			selected[nth] = i;
			combination(nth + 1, i + 1, selected);
		}
	}
	
	private static void bfs(Queue<int[]> queue, boolean[][] visited) {
		int remains = empties;
		int time = 1;
		
		while(!queue.isEmpty()) {
			int size = queue.size();
			
			while(size-- > 0) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];
				
				for(int d = 0; d < 4; d++) {
					int ny = cy + dy[d];
					int nx = cx + dx[d];
					
					if(isIn(ny, nx) && lab[ny][nx] != WALL && !visited[ny][nx]) {
						visited[ny][nx] = true;
						
						if(lab[ny][nx] == EMPTY) {
							remains--;
							if(remains == 0) {
								answer = Math.min(answer, time);
							}
						}
						
						queue.offer(new int[] {ny, nx});
					}
				}
			}
			
			time++;
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```

### **BOJ 13308 주유소**

[13308번: 주유소](https://boj.ma/13308/t)

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
	private static final int MAX_OIL = 2_500;
    private static final long INF = Long.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    도시 개수
    private static int M;    //    도로 개수
    
    private static List<int[]>[] graph;
    private static int[] costs;	//	cost[v] : v 도시 주유소의 기름값
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        costs = new int[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int v = 1; v <= N; v++)
        	costs[v] = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int v = 1; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int d = Integer.parseInt(st.nextToken());	//	두 도시 v1, v2 사이의 간선의 길이
            
            graph[v1].add(new int[] {v2, d});
            graph[v2].add(new int[] {v1, d});
        }
        
        System.out.println(dijkstra());
    }    //    main-end
    
    private static long dijkstra() {
        PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
        long[][] dist = new long[MAX_OIL + 1][N + 1];	//	dist[minOilCost][v] : v까지 오면서 가장 저렴한 주유소의 기름 가격이 minOilCost
        long res = INF;
        
        for(int k = 0; k <= MAX_OIL; k++)
        	Arrays.fill(dist[k], INF);
        
        dist[costs[1]][1] = 0;
        pq.offer(new long[] {1, dist[costs[1]][1], costs[1]});
        
        while(!pq.isEmpty()) {
            long[] cur = pq.poll();
            int cv = (int)cur[0];				//	cv까지 오는데	
            long cc = cur[1];					//	cc의 비용으로 옴
            int cMinOilCost = (int)cur[2];	 	//	cv까지 오는데 가장 싼 주유소 기름 가격이 cMinOilCost였음
            
            if(cv == N)	//	N에 도착한 경우
            	return cc;
            
            for(int[] edge : graph[cv]) {
            	int nv = edge[0];
            	int nd = edge[1];
            	
            	//	nv까지 cMinOilCost로 기름 사서 가는게 더 싼 값이 이미 알려진 경우
            	if(dist[cMinOilCost][nv] <= cc + nd * cMinOilCost)
            		continue;
            	
            	dist[cMinOilCost][nv] = cc + nd * cMinOilCost;
            	pq.offer(new long[] {nv, dist[cMinOilCost][nv], Math.min(cMinOilCost, costs[nv])});
            }
        }
        
        return -1;
    }
}    //    Main-class-end
```