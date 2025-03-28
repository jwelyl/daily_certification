# 25_02_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1153 네 개의 소수

[1153번: 네 개의 소수](http://boj.ma/1153/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static final List<Integer> primes = new ArrayList<>();	//	N 이하 소수 목록
	
	private static final List<Integer> answer = new ArrayList<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		if(N < 8) {	//	네 소수의 합은 최소 8임 (가장 작은 소수 = 2)
			System.out.println(-1);
			return;
		}

		answer.add(2);
		//	X >= 4인 짝수 X는 반드시 두 소수의 합으로 나타낼 수 있음
		if(N % 2 == 0) {		//	N이 짝수일 경우
			N -= (2 + 2);		//	N = 2 + 2 + X, 4 이상의 짝수 X를 두 소수의 합으로 나타내야함
			answer.add(2);
		}
		else {					//	N이 홀수일 경우
			N -= (2 + 3);		//	N = 2 + 3 + X, 4 이상의 짝수 X를 두 소수의 합으로 나타내야함
			answer.add(3);
		}
		
		findPrimes();
		
		int start = 0;
		int end = primes.size() - 1;
		
		while(start <= end) {
			int left = primes.get(start);
			int right = primes.get(end);
			
			if(left + right == N) {
				answer.add(left);
				answer.add(right);
				break;
			}
			
			if(left + right < N)	//	합이 N보다 작을 경우, 작은 소수를 더 키워서 합을 키움
				start++;
			else				//	합이 N보다 클 경우, 큰 소수를 더 줄여서 합을 줄임
				end--;
		}
		
		if(answer.size() < 4)
			System.out.println(-1);
		else {
			Collections.sort(answer);
			for(int prime : answer)
				System.out.print(prime + " ");
			System.out.println();
		}
	} //	main-end

	//	N 이하의 소수를 모두 찾기
	private static void findPrimes() {
		boolean[] sieve = new boolean[N + 1];	//	sieve[i] : i가 소수가 아니면 true
		sieve[1] = true;
		
		for(int i = 2; i <= N; i++) {
			if(!sieve[i]) {	//	i가 소수이면
				primes.add(i);
				
				for(long j = (long)i * i; j <= N; j += i)
					sieve[(int)j] = true;
			}
		}
	}
} //	Main-class-end
```