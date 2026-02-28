# 26_02_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15487 A[j]-A[i]+A[l]-A[k]

[15487번: A[j]-A[i]+A[l]-A[k]](http://boj.ma/15487/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st;
        
        int n = Integer.parseInt(br.readLine());
        int[] arr = new int[n];
        
        //    prefixDp[i] : 0 ~ i까지 arr[b] - arr[a] (0 <= a < b <= i)의 최댓값
        int[] prefixDp = new int[n];
        //    suffixDp[i] : i ~ n - 1까지 arr[d] - arr[c] (i <= c < d < n) 의 최댓값
        int[] suffixDp = new int[n];
        //    prefixMin[i] : 0 ~ i까지 arr[i]의 최솟값
        int[] prefixMin = new int[n];
        //    suffixMax[i] : i ~ n - 1까지 arr[i]의 최댓값
        int[] suffixMax = new int[n];
        int answer = Integer.MIN_VALUE;
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            arr[i] = Integer.parseInt(st.nextToken());
        
        prefixDp[1] = arr[1] - arr[0];
        prefixMin[0] = arr[0];
        prefixMin[1] = Math.min(prefixMin[0], arr[1]);
        
        for(int i = 2; i < n; i++) {
            prefixDp[i] = Math.max(prefixDp[i - 1], arr[i] - prefixMin[i - 1]);
            prefixMin[i] = Math.min(prefixMin[i - 1], arr[i]);
        }
        
        suffixDp[n - 2] = arr[n - 1] - arr[n - 2];
        suffixMax[n - 1] = arr[n - 1];
        suffixMax[n - 2] = Math.max(suffixMax[n - 1], arr[n - 2]);
        
        for(int i = n - 3; i >= 0; i--) {
            suffixDp[i] = Math.max(suffixDp[i + 1], suffixMax[i + 1] - arr[i]);
            suffixMax[i] = Math.max(suffixMax[i + 1], arr[i]);
        }
        
        for(int i = 1; i <= n - 3; i++)
            answer = Math.max(answer, prefixDp[i] + suffixDp[i + 1]);
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```