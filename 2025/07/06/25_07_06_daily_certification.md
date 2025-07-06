# 25_07_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17942 알고리즘 공부

[17942번: 알고리즘 공부](http://boj.ma/17942/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	알고리즘 개수
	private static int M;	//	목표 알고리즘 개수
	private static int R;	//	알고리즘 사이 관계 수
	
	private static final TreeSet<Algorithm> set = new TreeSet<>();
	private static Algorithm[] algoArr;
	private static List<Edge>[] graph;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		algoArr = new Algorithm[N + 1];
		graph = new ArrayList[N + 1];
		for(int num = 1; num <= N; num++)
			graph[num] = new ArrayList<>();
		
		st = new StringTokenizer(br.readLine());
		for(int num = 1; num <= N; num++) {
			algoArr[num] = new Algorithm(num, Integer.parseInt(st.nextToken()));
			set.add(algoArr[num]);
		}
		
		R = Integer.parseInt(br.readLine());
		for(int r = 0; r < R; r++) {
			st = new StringTokenizer(br.readLine());
			graph[Integer.parseInt(st.nextToken())].add(new Edge(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
		}
		
		while(M-- > 0) {
			Algorithm cur = set.pollFirst();		//	가장 공부량이 적게 필요한 알고리즘 꺼내기
			answer = Math.max(answer, cur.req);
			
			for(Edge edge : graph[cur.number]) {
				int number = edge.number;
				int dec = edge.dec;
				
				Algorithm next = algoArr[number];	//	cur 알고리즘과 연관 있는 알고리즘

				if(set.contains(next) ) {
					set.remove(next);
					next.req = Math.max(next.req - dec, 0);	//	cur 알고리즘 학습으로 인해 next 알고리즘 필요 공부량 감소
					set.add(next);
				}
			}
		}
		
		System.out.println(answer);
	} //	main-end
	
	private static class Algorithm implements Comparable<Algorithm> {
		public int number;
		public int req;
		
		public Algorithm(int number, int req) {
			this.number = number;
			this.req = req;
		}

		@Override
		public int compareTo(Algorithm other) {
			int res = Integer.compare(this.req, other.req);
			
			if(res == 0)
				res = Integer.compare(this.number, other.number);
			
			return res;
		}
	}
	
	private static class Edge {
		public int number;
		public int dec;
		
		public Edge(int number, int dec) {
			this.number = number;
			this.dec = dec;
		}
	}
} //	Main-class-end
```