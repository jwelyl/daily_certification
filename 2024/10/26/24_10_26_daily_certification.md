# 24_10_26_daily_certification

```
[#300 koreii] 데일리인증 202410126
1. Java Multithread, Concurrency + OS
- 메모리 가시성
- volatile
- 동시성 문제/임계 영역
- synchronized
2. 코딩 테스트 대비 알고리즘 학습
- Sorting (14746 Closest Pair)
```

# Problem Solving (Algorithm & SQL)

### BOJ 14746 Closest Pair

[](https://www.acmicpc.net/problem/14746)

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
	
	private static int N;	//	P에 속한 점 개수
	private static int M;	//	Q에 속한 점 개수
	
	private static int py;	//	P에 속한 점들의 y 좌표
	private static int qy;	//	Q에 속한 점들의 x 좌표
	
	private static final List<int[]> xposList = new ArrayList<>();
	
	private static int minDist = Integer.MAX_VALUE;
	private static int minDistCnt = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		py = Integer.parseInt(st.nextToken());
		qy = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			xposList.add(new int[] {Integer.parseInt(st.nextToken()), 0});
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < M; i++)
			xposList.add(new int[] {Integer.parseInt(st.nextToken()), 1});
		
		//	x 좌표가 작은 순서대로 정렬
		Collections.sort(xposList, (xpos1, xpos2) -> Integer.compare(xpos1[0], xpos2[0]));
		
		for(int i = 0; i < xposList.size() - 1; i++) {
			if(xposList.get(i)[1] != xposList.get(i + 1)[1]) {	//	인접한 두 점이 속한 집합이 다를 경우
				int dist = xposList.get(i + 1)[0] - xposList.get(i)[0];
				
				if(dist < minDist) {
					minDist = dist;
					minDistCnt = 1;
				}
				else if(dist == minDist)
					minDistCnt++;
			}
		}
		
		System.out.println((minDist + Math.abs(qy - py)) + " " + minDistCnt);
	}	//	main-end
}	//	Main-class-end
```

### BOJ 12991 홍준이의 행렬

[](https://www.acmicpc.net/problem/12991)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;        //    수열 A, B의 원소 개수
    private static long K;       //    K번째 수 (1 ~ N^2)
    
    private static long[] A;
    private static long[] B;
    
    private static long minA = Integer.MAX_VALUE;
    private static long maxA = Integer.MIN_VALUE;
    
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
        for(int i = 0; i < N; i++)
            B[i] = Integer.parseInt(st.nextToken());
        
        Arrays.sort(B);
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    //    K번째 수 res 반환
    private static long parametricSearch() {
        long start = 1L * minA * B[0];
        long end = 1L * maxA * B[N - 1];
        long res = 0L;
        
        while(start <= end) {
            long mid = (start + end) / 2;    //    K번째 수를 mid라고 가정
            
            long cnt = 0L;    //    mid 이하의 수 개수
            
            for(long num : A) {    //    A에 있는 수 num에 대해
                long q = mid / num;     //    mid를 num으로 나눈 몫
                cnt += binarySearch(q, B);    //    B에 존재하는 q 이하의 수의 개수가 num으로 만들 수 있는 mid 이하의 수 개수임
            }
            
            if(cnt >= K) {        //    mid 이하의 수가 K개 이상일 경우
                res = mid;        //    일단 mid를 저장
                end = mid - 1;    //    mid 이하의 수가 정확히 K가 될때까지 mid를 줄이기
            }
            else                    //    mid 이하의 수가 K개를 넘을 경우
                start = mid + 1;    //    mid 이하의 수가 K가 될때까지 mid를 키우기
        }
        
        return res;
    }
    
    //    nums에 존재하는 target 이하의 수의 개수
    private static long binarySearch(long target, long[] nums) {
        int start = 0;
        int end = N - 1;
        long idx = -1;    //    target 이하의 수 중 가장 오른쪽에 있는 수의 위치
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(nums[mid] > target)    //    mid에 있는 수가 target보다 클 경우
                end = mid - 1;        //    mid를 줄여야함
            else {    //    mid에 있는 수가 target보다 작거나 같을 경우
                idx = mid;            //    일단 mid를 idx에 저장
                start = mid + 1;      //    더 오른쪽에서 찾아보기
            }
        }
        
        return idx + 1;
    }
}    //    Main-class-end
```