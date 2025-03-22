# 25_03_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12738 가장 긴 증가하는 부분 수열 3

[12738번: 가장 긴 증가하는 부분 수열 3](http://boj.ma/12738/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static final List<Integer> lis = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        lis.add(Integer.MIN_VALUE);
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int num = Integer.parseInt(st.nextToken());
            
            if(num > lis.get(lis.size() - 1))
                lis.add(num);
            else {
                int idx = binarySearch(num);
                
                lis.set(idx, num);
            }
        }
        
        System.out.println(lis.size() - 1);
    }    //    main-end
    
    private static int binarySearch(int target) {
        int start = 0;
        int end = lis.size() - 1;
        int res = -1;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(lis.get(mid) >= target) {
                res = mid;
                end = mid - 1;
            }
            else
                start = mid + 1;
        }
        
        return res;
    }
}    //    Main-class-end
```