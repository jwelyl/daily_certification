# 24_08_05_daily_certification

```
[koreii] #218 데일리인증
20240805
Java 서적 독서
- T 메모리, 스택 프레임
- 지역 변수, 전역 변수, 멤버 변수
Java 멀티스레드, 동시성
- Thread 클래스 상속
- Runnalbe 인터페이스 구현
알고리즘 & 코딩 테스트 대비
- Dijkstra, Primality Test
```

# Problem Solving (Algorithm & SQL)

### BOJ 14431 소수마을

[](https://www.acmicpc.net/problem/17085)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_DIST = 8_500;
	private static final boolean[] sieve = new boolean[MAX_DIST];	//	sieve[d] : d가 소수이면 false, 소수가 아니면 true
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int X1;
	private static int Y1;	//	시작점 (0번)
	private static int X2;
	private static int Y2;	//	도착점 (N+1번)
	
	private static int N;	//	경유 가능한 점 개수
	private static int[] xPos;
	private static int[] yPos;	//	점 X, Y 좌표
	
	private static List<Node>[] graph;
	private static int[] dist;	//	dist[v] : 0번 점에서 v번 점까지의 최단 거리
	
	public static void main(String[] args) throws IOException {
		init();
		
		st = new StringTokenizer(br.readLine());
		X1 = Integer.parseInt(st.nextToken());
		Y1 = Integer.parseInt(st.nextToken());
		X2 = Integer.parseInt(st.nextToken());
		Y2 = Integer.parseInt(st.nextToken());
		
		N = Integer.parseInt(br.readLine());
		xPos = new int[N + 2];
		yPos = new int[N + 2];
		graph = new ArrayList[N + 2];
		dist = new int[N + 2];
		
		xPos[0] = X1;
		yPos[0] = Y1;
		xPos[N + 1] = X2;
		yPos[N + 1] = Y2;
		
		for(int i = 0; i < N + 2; i++) {
			graph[i] = new ArrayList<>();
			dist[i] = Integer.MAX_VALUE;
		}
		
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			xPos[i] = Integer.parseInt(st.nextToken());
			yPos[i] = Integer.parseInt(st.nextToken());
		}
		
		for(int v1 = 0; v1 < N + 1; v1++) {
			for(int v2 = v1 + 1; v2 < N + 2; v2++) {
				int dist = (int)Math.sqrt((xPos[v1] - xPos[v2]) * (xPos[v1] - xPos[v2]) + (yPos[v1] - yPos[v2]) * (yPos[v1] - yPos[v2]));
			
				if(sieve[dist])	//	v1, v2 점 사이 거리가 소수가 아닐 경우
					continue;	//	v1, v2를 직접 연결할 수는 없음
				
				graph[v1].add(new Node(v2, dist));
				graph[v2].add(new Node(v1, dist));	//	그래프 생성
			}
		}
		
		dijkstra();
		
		System.out.println(dist[N + 1] == Integer.MAX_VALUE ? -1 : dist[N + 1]);
	}	//	main-end
	
	private static void dijkstra() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		dist[0] = 0;
		pq.offer(new Node(0, dist[0]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int cv = cur.vertex;
			int cc = cur.cost;
			
			if(cc > dist[cv])	//	cv를 거치는 것이, 이미 알려진 cv까지의 최단거리를 이용하는 것보다 불리할 경우
				continue;
			
			for(Node next : graph[cv]) {
				int nv = next.vertex;
				int nc = cc + next.cost;	//	cv를 거쳐서 nv로 가는 비용
				
				if(nc < dist[nv]) {	//	cv를 거쳐가는게 더 비용이 작을 경우
					dist[nv] = nc;	//	비용 갱신
					pq.offer(new Node(nv, dist[nv]));
				}
			}
		}
	}
	
	private static void init() {
		sieve[0] = true;
		sieve[1] = true;	//	0, 1은 소수가 아님
		
		for(int num = 2; num * num < MAX_DIST; num++) {
			if(!sieve[num]) {	//	num이 소수일 경우
				for(int nnum = num * num; nnum < MAX_DIST; nnum += num)	//	num의 배수들을 모두 합성수 처리
					sieve[nnum] = true;
			}
		}
	}
	
	private static class Node implements Comparable<Node> {
		public int vertex;	//	점 번호
		public int cost;	//	vertex까지의 거리
		
		public Node(int vertex, int cost) {
			this.vertex = vertex;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Node other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```