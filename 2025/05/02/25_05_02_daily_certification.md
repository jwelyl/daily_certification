# 25_05_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 33543 둘이 한 팀

[33543번: 둘이 한 팀](http://boj.ma/33543/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static long sumA = 0;			//	A의 누적합
	private static long[] diffBA;			//	diffBA[i] : Bi - Ai 값 (크기 순 정렬)
	private static long[] prefixSumDiff;	//	prefixSumDiff[i] : diffAB의 1 ~ i까지의 누적합
	
	private static int Q;
	private static long incA = 0;			//	incA : 1 ~ q번째 쿼리 동안 A의 능력치 증가량 누적합
	private static long incB = 0;			//	incB : 1 ~ q번째 쿼리 동안 B의 능력치 증가량 누적합
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		diffBA = new long[N + 1];
		prefixSumDiff = new long[N + 1];
		
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			int a = Integer.parseInt(st.nextToken());
			int b = Integer.parseInt(st.nextToken());
			
			sumA += a;
			diffBA[i] = b - a;
		}
		
		diffBA[0] = Integer.MIN_VALUE;	//	정렬을 위해
		Arrays.sort(diffBA);
		diffBA[0] = 0;					//	원상복구
		
		for(int i = 1; i <= N; i++)
			prefixSumDiff[i] = prefixSumDiff[i - 1] + diffBA[i];	//	정렬된 상태에서 누적합 구하기
		
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 1; q <= Q; q++) {
			st = new StringTokenizer(br.readLine());
			char ch = st.nextToken().charAt(0);
			int inc = Integer.parseInt(st.nextToken());
			
			if(ch == 'A')
				incA += inc;
			else
				incB += inc;
			
			long res = sumA + incA * N;	//	Ai + A증가량 합 (1 <= i <= N)
			long target = incA - incB;
			
			int idx = lowerBound(target);	//	diffBA[idx]부터 diffBA[N]까지가 target보다 큼
			
			res += ((prefixSumDiff[N] - prefixSumDiff[idx - 1]) - target * (N - idx + 1));
		
			sb.append(res).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	//	1 <= i <= N일때, diffBA[i] > target인 i의 최솟값을 구함
	private static int lowerBound(long target) {
		int start = 1;
		int end = N;
		int res = N + 1;	//	diffBA[i] > target인 i가 존재하지 않으면 N + 1
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(diffBA[mid] > target) {
				res = mid;
				end = mid - 1;
			}
			else
				start = mid + 1;
		}
		
		return res;
	}
} //	Main-class-end
```