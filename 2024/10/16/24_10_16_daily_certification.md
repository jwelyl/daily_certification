# 24_10_16_daily_certification

```
[#290 koreii] 데일리인증 20241016
1. 경력 면접 준비
2. JPA 복습
- 영속성 컨텍스트
- 프록시, 지연 로딩, 영속성 전이
3. 알고리즘 & 코딩 테스트 대비 (복습)
- Greedy, Priority Queue (1715 카드 정렬하기)
- Two Pointer, Sorting (1253 좋다)
```

# Problem Solving (Algorithm & SQL)

### BOJ 1715 **카드 정렬하기**

[](https://www.acmicpc.net/problem/1715)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    private static final PriorityQueue<Long> pq = new PriorityQueue<>();
    
    private static long ans = 0L;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++)
            pq.offer(Long.parseLong(br.readLine()));

        while(pq.size() >= 2) {
            long card1 = pq.poll();
            long card2 = pq.poll();
            
            ans += (card1 + card2);
            pq.offer(card1 + card2);
        }
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1253 좋다

[](https://www.acmicpc.net/problem/1253)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int[] nums;
    
    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        nums = new int[N];
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            nums[i] = Integer.parseInt(st.nextToken());
        
        Arrays.sort(nums);
        
        for(int i = 0; i < N; i++) {
            int target = nums[i];
            
            int start = 0;
            int end = N - 1;
            
            while(start < end) {
                //    start와 end에 있는 두 수의 합이 target과 같다면
                if(nums[start] + nums[end] == target) {
                    if(start == i)       //    start가 i와 같을 경우
                        start++;
                    else if(end == i)    //    end가 i와 같을 경우    
                        end--;
                    else {    //    start, end가 i와 다를 경우, 조건 만족
                        ans++;
                        break;
                    }
                }
                //    start와 end에 있는 두 수의 합이 target보다 작다면
                else if(nums[start] + nums[end] < target)
                    start++;    //    합을 키워야 함
                //    start와 end에 있는 두 수의 합이 target보다 크다면
                else
                    end--;    //    합을 줄여야 함
            }
        }
            
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```