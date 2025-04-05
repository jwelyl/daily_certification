# 25_04_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2613 숫자구슬

[2613번: 숫자구슬](http://boj.ma/2613/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int[] beads;
	
	private static int min = 0;
	private static int max = 0;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		beads = new int[N];
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++) {
			beads[i] = Integer.parseInt(st.nextToken());
			min = Math.max(min, beads[i]);	//	구슬 하나를 구간으로 했을때 최대 크기
			max += beads[i];				//	모든 구슬 합 
		}
	
		parametricSearch();
	}	// main-end
	
	private static void parametricSearch() {
		int start = min;
		int end = max;
		int res = 0;
		
		while(start <= end) {
			int mid = (start + end) / 2;
			int cnt = cnt(mid);	//	구간합의 허용 최댓값을 mid로 했을때 구간의 최소 개수
			
			if(cnt > M)	//	M개보다 더 많은 구간이 필요할 경우 허용 최댓값을 더 크게 해야 함
				start = mid + 1;
			else {	//	M개 이하의 구간이 필요할 경우, 일단 저장하고 허용 최댓값을 더 작게 해보기
				res = mid;
				end = mid - 1;
			}
		}
		
		sb.append(res).append("\n");
		
		int cnt = 0;
		int ps = 0;
		
		for(int i = 0; i < N; i++) {
			ps += beads[i];
			
			if(ps > res) {	//	cnt + 1개의 구슬 합이 res보다 클 경우
				M--;			//	필요 구간 1개 감소함
				ps = beads[i];	//	새로운 구간
				sb.append(cnt).append(" ");
				cnt = 1;			//	새로운 구간 구슬 개수
			}
			else
				cnt++;
			
			if(M == N - i)	//	남은 필요 구간 수가 구슬 개수와 같을 경우
				break;
		}
		
		while(M-- > 0) {
			sb.append(cnt).append(" ");
			cnt = 1;
		}
		
		System.out.println(sb);
	}

	//	구간합의 최댓값이 maxPs를 넘지 않도록 구간을 나눌때 필요한 구간의 개수	
	//	구간합이 maxPs를 넘지 않는 선에서 구간의 크기를 최대한으로 유지해서 구간 개수 최소화하기
	private static int cnt(int maxPs) {
		int res = 1;
		int ps = 0;
		
		for(int i = 0; i < N; i++) {
			ps += beads[i];
			
			if(ps > maxPs) {
				res++;
				ps = beads[i];
			}
		}
		
		return res;
	}
} // Main-class-end
```