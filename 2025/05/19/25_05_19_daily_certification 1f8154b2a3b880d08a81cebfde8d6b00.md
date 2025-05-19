# 25_05_19_daily_certification

# To Do List

- [ ]  백준 1문제
- [ ]  Algoduck 채점 서버 구현
- [ ]  SQLD 문제 풀이
- [ ]  리눅스마스터 2급 2차 공부

```
[#139 koreii] 데일리인증 20250519
1. 프로젝트
1-1. React
- React 컴포넌트 분리, 주요 페이지 리팩토링
- Offset Pagination 구현 / 커스텀 훅 구현
- 문제 풀이 페이지에서 공개 테스트케이스 보여주기 추가
1-2. Spring
- 문제 상세 조회 시 공개 테스트케이스 포함한 DTO 구현
- Controller에서 Entity -> DTO로 수정
- 공개 테스트케이스의 경우 입/출력 데이터 DB에 저장하도록 DB 구현 + 초기 DML 수정
- ddl-auto 업데이트 시 발생한 metadata lock 문제 해결
2. 코딩 테스트 대비 문제 풀이
- Sieve Of Eratosthenes, Binary Search, Prefix Sum (BOJ 14941 호기심)
```

# Problem Solving (Algorithm & SQL)

### BOJ 14941 호기심

[14941번: 호기심](http://boj.ma/14941/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 100_000;
	private static final boolean[] SIEVE = new boolean[MAX + 1];
	
	private static final List<Integer> PRIMES = new ArrayList<>();
	
	private static long[] prefixSumOdd;
	private static long[] prefixSumEven;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	
	public static void main(String[] args) throws IOException {
		init();
		
		N = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < N; q++) {
			st = new StringTokenizer(br.readLine());
			
			int left = Integer.parseInt(st.nextToken());
			int right = Integer.parseInt(st.nextToken());
			int leftIdx = binarySearch(left, true);
			int rightIdx = binarySearch(right, false);
			
			long answer = 0;
			
			long psEven = prefixSumEven[rightIdx] - prefixSumEven[leftIdx - 1];
			long psOdd = prefixSumOdd[rightIdx] - prefixSumOdd[leftIdx - 1];
			
			if(leftIdx % 2 == 0)
				answer = 3 * psEven - psOdd;
			else
				answer = 3 * psOdd - psEven;
			
			sb.append(answer).append("\n");
		}
		
		System.out.print(sb);
	} // main-end
	
	//	lowerBound가 true일 경우, target보다 크거나 같으면서 가장 작은 소수의 index 반환
	//	lowerBound가 false일 경우, target보다 작거나 같으면서 가장 작은 소수의 index 반환
	private static int binarySearch(int target, boolean lowerBound) {
		int start = 1;
		int end = PRIMES.size() - 1;
		int res = -1;
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(PRIMES.get(mid) == target)
				return mid;
			
			if(lowerBound) {
				if(PRIMES.get(mid) > target) {
					res = mid;
					end = mid - 1;
				}
				else start = mid + 1;
			}
			else {
				if(PRIMES.get(mid) < target) {
					res = mid;
					start = mid + 1;
				}
				else end = mid - 1;
			}
		}
		
		return res;
	}
	
	private static void init() {
		PRIMES.add(0);
		SIEVE[1] = true;	//	1은 소수 아님
		
		for(int i = 2; i * i <= MAX; i++) {
			if(!SIEVE[i]) {	//	i가 소수일경우
				for(int j = i * i; j <= MAX; j += i)
					SIEVE[j] = true;	//	i의 배수들은 소수 아님
			}
		}
		
		for(int i = 1; i <= MAX; i++) {
			if(!SIEVE[i])
				PRIMES.add(i);
		}
		
		prefixSumOdd = new long[PRIMES.size()];
		prefixSumEven = new long[PRIMES.size()];
		
		for(int i = 1; i < PRIMES.size(); i++) {
			prefixSumOdd[i] = i % 2 == 1 ? PRIMES.get(i) : 0;
			prefixSumEven[i] = i % 2 == 0 ? PRIMES.get(i) : 0;
		}
		
		for(int i = 1; i < PRIMES.size(); i++) {
			prefixSumOdd[i] += prefixSumOdd[i - 1];
			prefixSumEven[i] += prefixSumEven[i - 1];
		}
	}
} // Main-class-end
```