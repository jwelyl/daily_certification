# 24_11_21_daily_certification

```
[#326 koreii] 데일리인증 20241121
1. SWOT 분석
2. 코딩 테스트 대비 알고리즘 학습
- Prefix Sum, Bruteforcing, Number Theory (24620 Sleeping in Class)
```

# Problem Solving (Algorithm & SQL)

### **BOJ** 24620 Sleeping in Class

[24620번: Sleeping in Class](https://boj.ma/24620/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int T;	//	테스트 케이스 개수
	private static int N;
	
	private static int[] prefixSum;	//	누적합 (N개 수의 합이 10^6 이하임이 보장됨)
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			N = Integer.parseInt(br.readLine());
			
			boolean allSame = true;	//	N개의 수가 모두 같음
			prefixSum = new int[N + 1];
			st = new StringTokenizer(br.readLine());
			prefixSum[1] = Integer.parseInt(st.nextToken());
			int before = prefixSum[1];
			
			for(int i = 2; i <= N; i++) {
				int num = Integer.parseInt(st.nextToken());
				
				if(before != num)
					allSame = false;
				
				before = num;
				prefixSum[i] = prefixSum[i - 1] + num;
			}
			
			if(allSame)	//	모든 수가 같을 경우
				sb.append(0).append("\n");
			else
				sb.append(func()).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static int func() {
		int sum = prefixSum[N];
		int min = N - 1;	//	모든 수를 다 합쳐야 할 경우
		
		//	sum보다 작은 sum의 약수 div에 대하여
		for(int i = 2; i * i <= sum; i++) {
			if(sum % i != 0)
				continue;
			
			int left = i;
			int right = sum / i;
			
			min = Math.min(min, func(left));
			min = Math.min(min, func(right));
		}
	
		return min;
	}
	
	//	주어진 수들을 합이 partSum인 부분으로 나눌 수 있을 경우 N - (부분 개수)
	//	합이 partSum인 부분으로 나눌 수 없을 경우 N
	private static int func(int partSum) {
		int partCnt = 1;
		
		int start = 1;
		int end = start;
		
		while(end <= N) {
			if(partialSum(start, end) > partSum)	//	부분합이 partSum보다 클 경우, partSum으로 균등하게 나눌 수 없음
				break;
			else if(partialSum(start, end) < partSum)
				end++;
			else {
				if(end == N)	//	마지막까지 잘 나누어졌으면
					return N - partCnt;
				
				start = end + 1;
				end = start;
				partCnt++;
			}
		}
		
		return N;	//	나눠지지 않은 경우
	}
	
	private static int partialSum(int from, int to) {
		return prefixSum[to] - prefixSum[from - 1];
	}
}	//	Main-class-end
```