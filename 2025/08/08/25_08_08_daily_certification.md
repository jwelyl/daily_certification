# 25_08_08_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 코드트리 투어**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/codetree-tour/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final int INIT   = 100;		//	코드트리 랜드 건설
	private static final int CREATE = 200;		//	여행 상품 생성
	private static final int CANCEL = 300;		//	여행 상품 취소
	private static final int SELL   = 400;		//	최적의 여행 상품 판매
	private static final int CHANGE = 500;		//	여행 상품의 출발지 변경
	
	private static final int MAX_TRIP = 30_000;	//	여행 최대 개수이자 여행 ID 최댓값
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int Q;
	
	private static int N;	//	도시 개수
	private static int M;	//	도로 개수
	private static int S;	//	출발 지점
	
	private static List<Edge>[] graph;
	
	//	trips[id] : 여행 ID가 id인 여행 정보, 없거나 삭제되었으면 null
	private static final Trip[] trips = new Trip[MAX_TRIP + 1];
	
	//	출발지가 S일때의 여행 상품 집합
	private static final TreeSet<Product> products = new TreeSet<>();
	
	private static int[] dist;	//	S로부터 각 도시까지의 최단거리
	
	public static void main(String[] args) throws IOException {
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 1; q <= Q; q++) {
			st = new StringTokenizer(br.readLine());
			
			int cmd = Integer.parseInt(st.nextToken());
			
			switch(cmd) {
			case INIT:
				init();
				break;
			case CREATE:
				create();
				break;
			case CANCEL:
				cancel();
				break;
			case SELL:
				sb.append(sell()).append("\n");
				break;
			case CHANGE:
				change();
				break;
			}
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void init() {
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		S = 0;
		
		graph = new ArrayList[N];
		dist = new int[N];
		for(int v = 0; v < N; v++)
			graph[v] = new ArrayList<>();
		
		for(int e = 0; e < M; e++) {
			int v = Integer.parseInt(st.nextToken());
			int u = Integer.parseInt(st.nextToken());
			int w = Integer.parseInt(st.nextToken());
		
			graph[v].add(new Edge(u, w));
			graph[u].add(new Edge(v, w));
		}
		
		dijkstra();
	}
	
	private static void create() {
		int id = Integer.parseInt(st.nextToken());
		int revenue = Integer.parseInt(st.nextToken());
		int dest = Integer.parseInt(st.nextToken());
		
		trips[id] = new Trip(revenue, dest);
		
		//	현재 출발지가 S일때 dest까지 여행 시 여행 상품의 이익
		int profit = revenue - dist[dest];
		
		//	dest까지 도달 가능하고, 손해는 아닐 경우
		if(dist[dest] != INF && profit >= 0) {
			//	생성된 여행 정보를 바탕으로 여행 상품 생성
			Product product = new Product(id, profit);
			products.add(product);
		}
		
		
	}
	
	private static void cancel() {
		int id = Integer.parseInt(st.nextToken());
		
		if(trips[id] == null)	//	이미 존재하지 않는 여행일 경우
			return;
		
		int revenue = trips[id].revenue;
		int dest = trips[id].dest;
		
		int profit = revenue - dist[dest];
		
		//	여행정보로부터 복구해낸 여행상품 정보
		Product product = new Product(id, profit);
		
		products.remove(product);	//	해당하는 여행 상품 제거
		trips[id] = null;			//	여행 정보도 제거
	}
	
	private static int sell() {
		if(!products.isEmpty()) {
			Product product = products.first();	//	최적의 여행 상품
			products.remove(product);
			trips[product.id] = null;
			
			return product.id;
		}
		
		return -1;
	}
	
	private static void change() {
		int ns = Integer.parseInt(st.nextToken());
		
		if(S == ns)
			return;
		S = ns;
		
		products.clear();	//	출발지 변경에 따른 기존 여행 상품들 초기화
		dijkstra();			//	변경된 출발지에서의 각 도시까지 최단거리 재계산
		
		for(int id = 1; id <= MAX_TRIP; id++) {
			if(trips[id] != null) {
				int revenue = trips[id].revenue;
				int dest = trips[id].dest;
				
				//	현재 출발지가 S일때 dest까지 여행 시 여행 상품의 이익
				int profit = revenue - dist[dest];
				
				//	dest까지 도달 가능하고, 손해는 아닐 경우
				if(dist[dest] != INF && profit >= 0) {
					//	생성된 여행 정보를 바탕으로 여행 상품 생성
					Product product = new Product(id, profit);
					products.add(product);
				}
			}
		}
		
	}
	
	private static void dijkstra() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		Arrays.fill(dist, INF);
		dist[S] = 0;
		pq.offer(new Node(S, dist[S]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			int cc = cur.cost;
			
			if(dist[cv] < cc)
				continue;
			
			for(Edge edge : graph[cv]) {
				int nv = edge.vertex;
				int nc = cc + edge.weight;
				
				if(nc < dist[nv]) {
					dist[nv] = nc;
					pq.offer(new Node(nv, dist[nv]));
				}
			}
		}
	}
	
	private static class Edge {
		public int vertex;
		public int weight;
		
		public Edge(int vertex, int weight) {
			this.vertex = vertex;
			this.weight = weight;
		}
	}
	
	private static class Node implements Comparable<Node> {
		public int vertex;
		public int cost;
		
		public Node(int vertex, int cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Node other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
	
	private static class Trip {		//	여행 정보
		public final int revenue;	//	여행 매출
		public final int dest;		//	여행 목적지
	
		public Trip(int revenue, int dest) {
			this.revenue = revenue;
			this.dest = dest;
		}
	}
	
	private static class Product implements Comparable<Product> {	//	출발지가 S일때 여행 정보를 토대로 생성한 여행 상품
		public final int id;		//	여행 id
		public final int profit;	//	여행 상품 이익 (매출 - 비용)
		
		public Product(int id, int profit) {
			this.id = id;
			this.profit = profit;
		}
		
		@Override
		public int compareTo(Product other) {
			int res = Integer.compare(other.profit, this.profit);	//	이익이 큰 상품이 우선순위가 높음
		
			if(res == 0)	//	이익이 같을 경우
				res = Integer.compare(this.id, other.id);	//	id가 작은 상품이 우선순위가 높음
			
			return res;
		}
	}
}	//	Main-class-end
```