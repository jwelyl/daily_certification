# 25_01_24_daily_certification

```
[#024 koreii] 데일리인증 20250124
1. 프로젝트 리팩토링 
- 테스트용 클라이언트 React 구현
- API 테스트
2. 코딩 테스트 대비 알고리즘 학습 
- Prefix Sum, DP, Binary Search (BOJ 12003 Diamond Collector (Silver)) 
3. 정보처리기사 필기 (프로그래밍 언어 활용)
- 네트워크
```

# Problem Solving (Algorithm & SQL)

### **BOJ 12003 Diamond Collector (Silver)**

[12003번: Diamond Collector (Silver)](https://boj.ma/12003/t)

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
	private static int K;
	
	private static int[] diamonds;
	private static int[] maxLeft;
	private static int[] maxRight;
	
	private static int left;
	private static int right;
	
	private static int result = 0;
	
	public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());

        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());

        diamonds = new int[N];
        for (int i = 0; i < N; i++)
            diamonds[i] = Integer.parseInt(br.readLine());
        maxLeft = new int[N];
        maxRight = new int[N];
        
        // 1. 정렬
        Arrays.sort(diamonds);

        // 2. 투 포인터를 이용해 maxLeft 계산
        left = 0;
        maxLeft[0] = 1;
        for (right = 1; right < N; right++) {
            while (diamonds[right] - diamonds[left] > K)	//	right의 다이아몬드와 left의 다이아몬드 크기 차이가 K 이하가 될 때까지 left 이동
                left++;
            
            maxLeft[right] = Math.max(right - left + 1, maxLeft[right - 1]);
        }

        // 3. 투 포인터를 이용해 maxRight 계산
        right = N - 1;
        maxRight[N - 1] = 1;
        for (left = N - 2; left >= 0; left--) {
            while (diamonds[right] - diamonds[left] > K)	//	left의 다이아몬드와 right의 다이아몬드 크기 차이가 K 이하가 될 때까지 right 이동
                right--;
            
            maxRight[left] = Math.max(right - left + 1, maxRight[left + 1]);
        }

        // 4. maxLeft[i] + maxRight[i + 1] 계산
        for (int i = 0; i < N - 1; i++)
            result = Math.max(result, maxLeft[i] + maxRight[i + 1]);

        System.out.println(result);
    }	//	main-end
}	//	Main-class-end
```