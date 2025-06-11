# 25_06_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2593 엘리베이터

[2593번: 엘리베이터](http://boj.ma/2593/t)

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
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	건물 최대 층수
	private static int M;	//	엘리베이터 개수
	
	//	elevators[e] : e번째 엘리베이터가 멈추는 층 리스트
	private static List<Integer>[] elevators;
	//	floors[f] : f번째 층에 멈추는 엘리베이터 리스트
	private static List<Integer>[] floors;
	
	//	isEnd[e] : e번째 엘리베이터가 목적 층에 도달하면 true
	private static boolean[] isEnd;
	
	private static int A;	//	출발 층수
	private static int B;	//	도착 층수
	
	//	{현재 엘리베이터 번호, 지금까지 엘리베이터 탄 최소 횟수} 
	private static final PriorityQueue<Node> pq = new PriorityQueue<>();
	
	//	dist[e] : e번째 엘리베이터까지 엘리베이터를 탄 최소 횟수
	private static int[] dist;
	
	//	prevs[e] : A에서 B까지 엘리베이터를 최소로 갈아탔을때 e번째 엘리베이터 이전에 탄 엘리베이터
	private static int[] prevs;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		elevators = new ArrayList[M + 1];
		floors = new ArrayList[N + 1];
		
		for(int e = 0; e <= M; e++)
			elevators[e] = new ArrayList<>();
		for(int f = 0; f <= N; f++)
			floors[f] = new ArrayList<>();
		
		isEnd = new boolean[M + 1];
		dist = new int[M + 1];
		Arrays.fill(dist, INF);
		prevs = new int[M + 1];
		
		for(int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int start = Integer.parseInt(st.nextToken());
			int inc = Integer.parseInt(st.nextToken());
			
			//	e번째 엘리베이터가 갈 수 있는 층 목록
			for(int floor = start; floor <= N; floor += inc) {
				elevators[e].add(floor);
				floors[floor].add(e);
			}
		}
		
		st = new StringTokenizer(br.readLine());
		A = Integer.parseInt(st.nextToken());
		B = Integer.parseInt(st.nextToken());
		
		//	A층에 서는 엘리베이터 목록
		for(int e : floors[A]) {
			dist[e] = 1;
			pq.offer(new Node(e, 1));
		}
		
		//	B층에 서는 엘리베이터 목록
		for(int e : floors[B])
			isEnd[e] = true;
		
		dijkstra();
	}	//	main-end
	
	private static void dijkstra() {
		while (!pq.isEmpty()) {
			Node cur = pq.poll();
			int ce = cur.e;
			int cdist = cur.dist;

			if (dist[ce] < cdist)
				continue;

			for(int floor : elevators[ce]) {
				for (int j = 0; j < floors[floor].size(); j++) {
					int ne = floors[floor].get(j);

					if (dist[ne] > cur.dist + 1) {
						dist[ne] = cur.dist + 1;
						prevs[ne] = cur.e;

						pq.offer(new Node(ne, dist[ne]));
					}
				}
			}
		}
		
		int minDist = INF;	//	B층에 도착할 수 있는 최소 엘리베이터 탑승 횟수
		int minE = INF;		//	그때 도착한 엘리베이터
		
		for(int e = 1; e <= M; e++) {
			if(isEnd[e] && dist[e] < minDist) {
				minDist = dist[e];
				minE = e;
			}
		}
		
		if(minDist == INF)
			sb.append("-1\n");
		else {
			sb.append(minDist).append("\n");
			
			int curE = minE;
			List<Integer> path = new ArrayList<>();
			
			while(curE != 0) {
				path.add(curE);
				curE = prevs[curE];
			}
			
			for(int i = path.size() - 1; i >= 0; i--)
				sb.append(path.get(i)).append("\n");
		}
		
		System.out.print(sb);
	}
	
	private static class Node implements Comparable<Node> {
		public int e;
		public int dist;

		public Node(int e, int dist) {
			this.e = e;
			this.dist = dist;
		}

		@Override
		public int compareTo(Node other) {
			return Integer.compare(this.dist, other.dist);
		}
	}
}	//	Main-class-end

```