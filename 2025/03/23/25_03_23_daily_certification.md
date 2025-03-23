# 25_03_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1818 책정리

[1818번: 책정리](http://boj.ma/1818/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    
    private static int lisLen = 1;    //    LIS 길이
    
    private static final List<Integer> lis = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        lis.add(-N);
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int num = Integer.parseInt(st.nextToken());
            
            if(lis.get(lis.size() - 1) < num)    //    lis에 저장된 마지막 수보다 더 클 경우
                lis.add(num);
            else {    //    num보다 큰 수 중 가장 왼쪽에 있는 수를 찾아 해당 위치에 num 대입
                int idx = binarySearch(num);
                
                if(idx == -1)    //    lis보다 작은 값이 없을 경우
                    continue;
                
                lis.set(idx, num);
            }
        }
        
        lisLen = lis.size() - 1;
        
        System.out.println(N - lisLen);
    }    //    main-end
    
    private static int binarySearch(int num) {
        int start = 0;
        int end = lis.size() - 1;
        int res = -1;    //    num보다 큰 수가 없을 경우
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(lis.get(mid) <= num)    //    mid번째 수가 num보다 작거나 같은 경우
                start = mid + 1;    //   mid를 더 키워야 함
            else {    //    mid번째 수가 num보다 더 클 경우
                res = mid;    //    일단 mid 저장
                end = mid - 1;    //    mid를 줄이기
            }
        }
        
        return res;
    }
}    //    Main-class-end
```

### BOJ 12738 가장 긴 증가하는 부분 수열 2

[12015번: 가장 긴 증가하는 부분 수열 2](https://boj.ma/12015/t)

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
        lis.add(-N);
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int num = Integer.parseInt(st.nextToken());
            
            if(num > lis.get(lis.size() - 1))
                lis.add(num);
            else {
                int idx = binarySearch(num);
                
                if(idx != -1)
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