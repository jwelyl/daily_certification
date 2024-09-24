# 24_09_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16987 **계란으로 계란치기**

[](https://www.acmicpc.net/problem/16987)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;        //    계란 개수
    private static int[] sArr;   //    계란 내구도
    private static int[] wArr;   //    계란 무게
    
    private static int maxCnt = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        sArr = new int[N];
        wArr = new int[N];
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            sArr[i] = Integer.parseInt(st.nextToken());
            wArr[i] = Integer.parseInt(st.nextToken());
        }
        
        dfs(0, 0);
        
        System.out.println(maxCnt);
    }    //    main-end
    
    //    hand : 손에 든 계란 index
    //    cnt : 깨진 계란 수
    private static void dfs(int hand, int cnt) {
        if(cnt > maxCnt)
            maxCnt = cnt;
        
        if(hand == N)   //   가장 오른쪽 계란까지 던졌을 경우
        	return;
        
        if(sArr[hand] <= 0) {        //    손에 든 계란이 깨진 경우
            dfs(hand + 1, cnt);      //    현재 계란은 넘어가고 오른쪽 계란으로 넘거가기
            return;
        }
        
        for(int target = 0; target < N; target++) {
            if(target == hand || sArr[target] <= 0)    //    target 계란이 깨진 경우
                continue;
            
            sArr[target] -= wArr[hand];
            sArr[hand] -= wArr[target];    //    hand, target 계란 모두 내구도 감소
            
            int broken = (sArr[target] <= 0 ? 1 : 0) + (sArr[hand] <= 0 ? 1 : 0);
            dfs(hand + 1, cnt + broken);
            
            sArr[target] += wArr[hand];
            sArr[hand] += wArr[target];
        }
    }
}    //    Main-class-end
```

### BOJ 3151 **합이 0**

[](https://www.acmicpc.net/problem/3151)

### Binary Search (O(N^2logN))

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
    
    private static long ans = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        nums = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            nums[i] = Integer.parseInt(st.nextToken());
        
        Arrays.sort(nums);
        
        for(int idx1 = 0; idx1 < N - 1; idx1++) {
            for(int idx2 = idx1 + 1; idx2 < N; idx2++) {
                int num1 = nums[idx1];
                int num2 = nums[idx2];
                int target = -num1 - num2;

                int cnt = cntOf(target);

                if(num1 == target)
                    cnt--;
                if(num2 == target)
                    cnt--;
                
                ans += cnt;
            }
        }
        
        System.out.println(ans / 3);
    }    //    main-end
    
    private static int cntOf(int num) {
        int leftIdx = binarySearch(num, false);     //    nums에서 num의 가장 왼쪽 index
        
        if(leftIdx == N)
            return 0;
        
        int rightIdx = binarySearch(num, true);    //    nums에서 num의 가장 오른쪽 index
        
        return rightIdx - leftIdx + 1;
    }
    
    private static int binarySearch(int num, boolean upper) {
        int start = 0;
        int end = N - 1;
        int ret = N;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(nums[mid] < num)    //    mid 값이 더 작을 경우, 더 오른쪽에서 찾아야 함
                start = mid + 1;
            else if(nums[mid] == num) {	//    mid 값이 num일 경우, 일단 mid 저장하고
                ret = mid;
                if(!upper)	//   	lower bound일 경우 일단 mid 저장하고 더 왼쪽에서 찾아보기 
                	end = mid - 1;
                else		//   	upper bound일 경우 일단 mid 저장하고 더 오른쪽에서 찾아보기
                	start = mid + 1;
            }
            else    //    mid 값이 더 클 경우, 더 왼쪽에서 찾아야 함
                end = mid - 1;
        }
        
        return ret;
    }
}    //    Main-class-end
```

### Two-Pointer (O(N^2))

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
    
    private static long ans = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        nums = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            nums[i] = Integer.parseInt(st.nextToken());
        
        Arrays.sort(nums);
        
        for(int idx1 = 0; idx1 < N - 2; idx1++) {
            int idx2 = idx1 + 1;
            int idx3 = N - 1;
            
            int num1 = nums[idx1];
            
            while(idx2 < idx3) {
                int num2 = nums[idx2];
                int num3 = nums[idx3];
                
                if(num2 + num3 > -num1)    //    세 수 합이 0보다 클 경우
                    idx3--;    //    값을 줄여야 함
                else if(num2 + num3 < -num1)    //    세 수 합이 0보다 작을 경우
                    idx2++;    //    값을 키워야 함
                else {    //    세 수 합이 0일 경우
                    if(num2 == num3) {    //    [idx2, idx3]까지 모두 같은 값일 경우
                        ans += (idx3 - idx2 + 1) * (idx3 - idx2) / 2;
                        break;
                    }
                    else {
                        int cntIdx2 = 1;    //    idx2의 값을 가지는 index 개수
                        int cntIdx3 = 1;    //    idx3의 값을 가지는 index 개수
                        
                        while(nums[idx2 + cntIdx2] == nums[idx2])
                            cntIdx2++;
                        while(nums[idx3 - cntIdx3] == nums[idx3])
                            cntIdx3++;
                        
                        ans += cntIdx2 * cntIdx3;
                        
                        idx2 += cntIdx2;
                        idx3 -= cntIdx3;
                    }
                }
            }
        }
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```