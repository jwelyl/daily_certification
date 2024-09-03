# 24_09_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12991 홍준이의 행렬

[](https://www.acmicpc.net/problem/12991)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static long K;
	
	private static long[] A;
	private static long[] B;
	
	private static long minA = Integer.MAX_VALUE;
	private static long maxA = Integer.MIN_VALUE;
	private static long minB = Integer.MAX_VALUE;
	private static long maxB = Integer.MIN_VALUE;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Long.parseLong(st.nextToken());
		
		A = new long[N];
		B = new long[N];
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++) {
			A[i] = Integer.parseInt(st.nextToken());
			minA = Math.min(minA, A[i]);
			maxA = Math.max(maxA, A[i]);
		}
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++) {
			B[i] = Integer.parseInt(st.nextToken());
			minB = Math.min(minB, B[i]);
			maxB = Math.max(maxB, B[i]);
		}
		
		Arrays.sort(B);
		
		System.out.println(parametricSearch());
	}	//	main-end
	
	private static long parametricSearch() {
		long start = minA * minB;
		long end = maxA * maxB;
		
		long ret = end;
		
		while(start <= end) {
			long mid = (start + end) / 2;
			
			if(cnt(mid) >= K) {	//	mid 이하의 수가 K개 이상일 경우
				ret = mid;	//	일단 mid 저장
				end = mid - 1;	//	더 작은 값으로 해보기 (mid가 딱 K번째 수일때)
			}
			else	//	mid 이하의 수가 K개가 되지 않을 경우
				start = mid + 1;	//	더 큰 값으로 해보기
		}
		
		return ret;
	}
	
	//	value 이하 수의 개수 반환
	private static long cnt(long value) {
		long cnt = 0;
		
		for(long num : A) {
			long target = value / num;
			
			cnt += binarySearch(target);	//	B 배열에서 target 이하의 수의 개수 구하기
		}
		
		return cnt;
	}
	
	private static long binarySearch(long target) {
		int start = 0;
		int end = N - 1;
		int idx = N;	//	target보다 크면서 가장 작은 수가 처음 나오는 index, N일 경우 모든 수가 target 이하라는 뜻임
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(B[mid] > target) {	//	mid index값이 target보다 클 경우
				idx = mid;	//	일단 idx 저장
				end = mid - 1;	//	더 작은 값이 있나 찾기
			}
			else	//	mid index값이 target보다 작을 경우
				start = mid + 1;	//	더 큰 값에서 찾아야 함
		}
		
		return idx;
	}
}	//	Main-class-end
```