# 25_01_26_daily_certification

```
[#026 koreii] 데일리인증 20250126
1. 코딩 테스트 대비 알고리즘 학습 
- BFS, Parametric Search (BOJ 2585 경비행기) 
```

# Problem Solving (Algorithm & SQL)

### **BOJ 2585 경비행기**

[2585번: 경비행기](https://boj.ma/2585/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int DST = 10_000;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int K;
	
	private static Point[] airports;

	public static void main(String[] args) throws Exception {
	    st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		airports = new Point[N];

		for (int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());
			airports[i] = new Point(x, y);
		}

		System.out.println(parametricSearch());
	}	//	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = cost(0, 0, DST, DST);
		int res = end;
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(bfs(mid)) {			//	mid 연료량으로 K번 이하 경유로 갈 수 있을 경우
				res = mid;			//	일단 mid 저장
				end = mid - 1;		//	더 적은 연료량 찾아보기	
			}
			else					//	mid 연료량으로 K번 이하 경유로 갈 수 없을 경우
				start = mid + 1;	//	더 많은 연료량 찾아보기
		}
		
		return res;
	}

	private static boolean bfs(int size) {
		Queue<Point> queue = new LinkedList<>();
		boolean[] visit = new boolean[N];

		queue.offer(new Point(0, 0, 0));

		while (!queue.isEmpty()) {
			Point cur = queue.poll();

			if (cost(cur.x, cur.y, DST, DST) <= size)	//	현재 위치에서 목적지까지 경유 없이 갈 수 있을 경우
				return true;

			if (cur.cnt == K)	//	더 이상 경유할 수 없을 경우
				continue;

			for (int i = 0; i < N; i++) {
				//	i번째 경유지를 이미 방문했거나 최대 연료량으로 갈 수 없는 경유지일경우
				if (visit[i] || size < cost(cur.x, cur.y, airports[i].x, airports[i].y))
					continue;
				visit[i] = true;
				queue.offer(new Point(airports[i].x, airports[i].y, cur.cnt + 1));
			}
		}

		return false;
	}

	//	(x1, y1), (x2, y2) 사이 이동하는데 필요한 연료 량
	private static int cost(int x1, int y1, int x2, int y2) {
		double result = Math.sqrt((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1));	//	두 점 사이 거리
		
		if(result / 10 == ((int)result) / 10)	//	나누어떨어질 경우
			return (int) result / 10;
		else	//	나누어떨어지지 않을 경우
			return (int) result / 10 + 1;
	}

	private static class Point {
		public int x;
		public int y;
		public int cnt;

		public Point(int x, int y) {
			this.x = x;
			this.y = y;
		}

		public Point(int x, int y, int cnt) {
			this.x = x;
			this.y = y;
			this.cnt = cnt;
		}
	}
}	//	Main-class-end
```