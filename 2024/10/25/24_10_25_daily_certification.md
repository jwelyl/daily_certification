# 24_10_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1781 컵라면

[](https://www.acmicpc.net/problem/1781)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static final List<int[]> works = new ArrayList<>();    //    과제 리스트, {데드라인, 보상}
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();    //    할 수 있는 과제의 보상 pq (보상 작은 과제부터 꺼냄)
    
    private static int N;            //    과제 개수
    private static int ans = 0;      //    얻을 수 있는 최대 보상
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            works.add(new int[] {Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())});
        }
        
        //    과제의 데드라인이 짧은 순으로 정렬
        Collections.sort(works, (w1, w2) -> Integer.compare(w1[0], w2[0]));
        
        for(int[] work : works) {
            int deadline = work[0];    //    과제의 데드라인
            int reward = work[1];      //    과제의 보상
            
            pq.offer(reward);    //    해당 과제를 했다고 치고 pq에 보상을 넣음
            
            if(pq.size() > deadline)    //    해야 하는 과제의 수가 과제의 데드라인보다 많을 경우, 어떤 과제를 포기해야 함
                pq.poll();    //    보상이 가장 작은 과제를 포기함
        }
        
        while(!pq.isEmpty())    //    pq에 남아있는 과제들이 할 수 있는 과제들임
            ans += pq.poll();
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
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

### **CO{)E TREE 회의실 겹치지 않게 하기**

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/do-not-overlap-the-meeting-room/description)

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
    private static final List<int[]> meetings = new ArrayList<>();
    
    private static int endTime = 0;
    private static int ans = 0;	//	열 수 있는 회의 최대 개수

    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());

        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int start = Integer.parseInt(st.nextToken());
            int end = Integer.parseInt(st.nextToken());

            meetings.add(new int[] {start, end});
        }

        //  회의가 가장 빨리 끝나는 순서대로 정렬
        //	회의 끝나는 시간이 같을 경우 더 빨리 시작하는 회의 선택
        Collections.sort(meetings, (m1, m2) -> Integer.compare(m1[1], m2[1]) == 0 ? Integer.compare(m1[0], m2[0]) : Integer.compare(m1[1], m2[1]));
        
        for(int[] m : meetings) {
        	int s = m[0];
        	int e = m[1];
        	
        	if(s >= endTime) {
        		ans++;
        		endTime = e;
        	}
        }
        
        System.out.println(N - ans);
    }   //  main-end
}   //  Main-class-end
```