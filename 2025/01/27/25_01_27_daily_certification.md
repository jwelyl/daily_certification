# 25_01_27_daily_certification

```
[#027 koreii] 데일리인증 20250127
1. 코딩 테스트 대비 알고리즘 학습 
- Meet in the middle, Binary Search, Subset (BOJ 1450 냅색문제)
2. 정보처리기사 필기 (정보시스템 구축관리)
- 소프트웨어 개발 방법론 개념 + 문제
- IT 프로젝트 정보시스템 구추관리 개념 + 문제
```

# Problem Solving (Algorithm & SQL)

### **BOJ 1450 냅색문제**

[1450번: 냅색문제](https://boj.ma/1450/t)

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
	private static long C;
	private static int[] weights;
	
	private static final List<Long> leftSums = new ArrayList<>();
	private static final List<Long> rightSums = new ArrayList<>();
	
	private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		
		weights = new int[N];
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			weights[i] = Integer.parseInt(st.nextToken());
		
		subset(0, (N - 1) / 2 + 1, 0, leftSums);
		subset((N - 1) / 2 + 1, N, 0, rightSums);
		
		Collections.sort(leftSums);
		Collections.sort(rightSums);
		
		for(long leftSum : leftSums) {
			if(leftSum > C)
				break;
			
			long limit = C - leftSum;
			
			answer += binarySearch(limit);
		}
		
		System.out.println(answer);
	} //	main-end
	
	//	limit 이하의 수의 개수
	private static int binarySearch(long limit) {
		int start = 0;
		int end = rightSums.size() - 1;
		int res = end + 1;
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(rightSums.get(mid) <= limit) {	//	mid의 값이 limit보다 작거나 같을 경우
				res = mid + 1;					//	일단 mid + 1 저장
				start = mid + 1;				//	mid 더 키워보기
			}
			else								//	mid의 값이 limit보다 더 클 경우
				end = mid - 1;					//	mid 더 줄여야 함
		}
		
		return res;
	}
	
	private static void subset(int nth, int size, long sum, List<Long> sums) {
		if(nth == size) {
			sums.add(sum);
			return;
		}
		
		subset(nth + 1, size, sum + weights[nth], sums);	//	nth번째 포함 o
		subset(nth + 1, size, sum, sums);					//	nth번째 포함 x
	}
} //	Main-class-end
```