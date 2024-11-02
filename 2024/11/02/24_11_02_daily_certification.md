# 24_11_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2109 순회강연

[](https://www.acmicpc.net/problem/2109)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static final List<int[]> lectures = new ArrayList<>();
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();
    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int l = 0; l < N; l++) {
            st = new StringTokenizer(br.readLine());
            
            int p = Integer.parseInt(st.nextToken());
            int d = Integer.parseInt(st.nextToken());
            
            lectures.add(new int[] {p, d});
        }
        
        Collections.sort(lectures, (l1, l2) -> Integer.compare(l1[1], l2[1]));
        
        for(int[] lecture : lectures) {
            int p = lecture[0];
            int d = lecture[1];
            
            pq.offer(p);
            
            if(d < pq.size())
                pq.poll();
        }
        
        while(!pq.isEmpty())
            ans += pq.poll();
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 13397 구간 나누기 2

[](https://www.acmicpc.net/problem/13397)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    배열의 크기
    private static int M;    //    가능한 최대 구간 수
    
    private static int[] nums;    //    배열의 수
    
    private static int min = 10_001;    //    전체 수 중 최솟값
    private static int max = 0;         //    전체 수 중 최댓값
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        nums = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            nums[i] = Integer.parseInt(st.nextToken());
            min = Math.min(min, nums[i]);
            max = Math.max(max, nums[i]);
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;            //    어떤 구간의 최댓값 == 최솟값일 경우
        int end = max - min;      //    구간이 하나일 경우 구간 점수의 최댓값은 max - min임
        int res = end;
        
        while(start <= end) {
            //    구간 점수의 최댓값이 mid라고 가정
            //    어떤 구간에서도 최댓값과 최솟값의 차이가 mid보다 커서는 안된다.
            int mid = (start + end) / 2;
            
            if(ok(mid)) {    //    구간 점수 최댓값이 mid 이하가 되도록 구간을 나눌때, M개 이하 구간이 될 경우
                res = mid;        //    일단 mid 저장
                end = mid - 1;    //    구간 점수 최댓값이 더 작아도 되는지 확인
            }
            else             //    구간 점수 최댓값이 mid 이하가 되도록 구간을 나눌때, M개 이하 구간으로 나눌 수 없을 경우
                start = mid + 1;    //    구간 개수를 M개 이하로 줄이기 위해서는 구간 점수 최댓값을 키워야 함
        }
        
        return res;
    }
    
    private static boolean ok(int diff) {
        int idx = 0;    //    구간의 시작 index
        int cnt = 1;    //    구간 개수
        
        while(idx < N) {    //    새로운 구간 시작
            int min = nums[idx];
            int max = nums[idx];    //    구간의 시작 값을 최대, 최소값으로 설정
        
            int end = idx + 1;
            
            while(end < N) {
                min = Math.min(min, nums[end]);
                max = Math.max(max, nums[end]);
                
                if(max - min <= diff)    //    end까지 구간에 포함될 경우
                    end++;               //    다음 수도 포함되나 확인해야 함
                else {    //    end를 포함하면 최댓값 - 최솟값 > diff일 경우
                    cnt++;    //    필요한 구간 수 1 증가
                    
                    if(cnt > M)    //    필요한 구간 수가 M개를 넘을 경우
                        return false;
                    
                    break;    //    end를 시작으로 다음 구간을 확인해야 함
                }
            }
            
            idx = end;
        }
        
        return true;
    }
}    //    Main-class-end
```