# 25_07_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32207 꿀잼 루비 문제

[32207번: 꿀잼 루비 문제](http://boj.ma/32207/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int K;
	
	private static List<Ruby> rubyList = new ArrayList<>();
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++)
				rubyList.add(new Ruby(y, x, Integer.parseInt(st.nextToken())));
		}
		
		Collections.sort(rubyList);
		rubyList = rubyList.subList(0, Math.min(rubyList.size(), 5 * K));	//	값이 가장 큰 5 * K개의 루비만 추출
		
		combination(0, 0, 0, new int[K]);
		
		System.out.println(answer);
	} //	main-end
	
	private static void combination(int nth, int start, int sum, int[] selected) {
		if(nth == K)
			return;
		
		for(int i = start; i < rubyList.size(); i++) {
			boolean canGet = true;
			
			for(int j = 0; j < nth; j++) {
				int before = selected[j];
				
				if(rubyList.get(i).isAdjacent(rubyList.get(before))) {
					canGet = false;
					break;
				}
			}
			
			if(canGet) {
				answer = Math.max(answer, sum + rubyList.get(i).value);
				selected[nth] = i;
				combination(nth + 1, i + 1, sum + rubyList.get(i).value, selected);
			}
		}
	}
	
	private static class Ruby implements Comparable<Ruby> {
		public int y;
		public int x;
		public int value;
		
		public Ruby(int y, int x, int value) {
			this.y = y;
			this.x = x;
			this.value = value;
		}
		
		@Override
		public int compareTo(Ruby other) {
			return Integer.compare(other.value, this.value);
		}
		
		public boolean isAdjacent(Ruby other) {
			return Math.abs(this.y - other.y) + Math.abs(this.x - other.x) == 1;
		}
	}
} //	Main-class-end
```