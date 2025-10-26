# 25_10_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22942 데이터 체커

[22942번: 데이터 체커](http://boj.ma/22942/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.Deque;
import java.util.ArrayDeque;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static final Set<Integer> xSet = new HashSet<>();
	private static final List<Segment> list = new ArrayList<>();
	private static final Deque<Segment> stack = new ArrayDeque<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			
			int x = Integer.parseInt(st.nextToken());
			int r = Integer.parseInt(st.nextToken());
			
			int x1 = x - r;
			int x2 = x + r;
			
			//	지름 양 끝 좌표 중 겹치는 게 있으면 반드시 교점 1개는 존재
			if(xSet.contains(x1) || xSet.contains(x2)) {
				System.out.println("NO");
				return;
			}
			
			xSet.add(x1);
			xSet.add(x2);
				
			list.add(new Segment(x1, x2));
		}
		
		Collections.sort(list);
		
		for(Segment cur : list) {
			int cx1 = cur.x1;
			int cx2 = cur.x2;
			
			while(!stack.isEmpty()) {
				Segment prev = stack.peekLast();
				int px2 = prev.x2;	//	cx1 > px1인것은 이미 보장됨
				
				if(cx1 > px2) {		//	cur이 prev 내부에 있지 않을 경우
					stack.pollLast();
					continue;
				}
				if(cx1 < px2) {
					if(cx2 < px2)	//	cur이 prev 내부에 포함될 경우
						break;
					else {
						System.out.println("NO");
						return;
					}
				}
			}
			
			stack.offerLast(cur);
		}
		
		System.out.println("YES");
	} //	main-end
	
	private static class Segment implements Comparable<Segment> {
		public int x1;
		public int x2;
		
		public Segment(int x1, int x2) {
			this.x1 = x1;
			this.x2 = x2;
		}
		
		@Override
		public int compareTo(Segment other) {
			return Integer.compare(this.x1, other.x1);
		}
	}
} //	Main-class-end
```