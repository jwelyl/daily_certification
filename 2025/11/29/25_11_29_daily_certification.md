# 25_11_29_daily_certification

# To Do List

```
[#333 koreii] 데일리인증 20251129
1. 알고리즘 문제 풀이
- BOJ 31501 DP (Small) (DP, LIS(O(N^2)))
```

```
[#152] 데일리인증 20251129 (333 / 365)
1. 알고리즘 문제 풀이
- BOJ 31501 DP (Small) (DP, LIS(O(N^2)))
```

# Problem Solving (Algorithm & SQL)

### BOJ 31501 DP (Small)

[31501번: DP (Small)](http://boj.ma/31501/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringBuilder sb = new StringBuilder();
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());    //    문제 개수
        int q = Integer.parseInt(st.nextToken());    //    쿼리 개수
        
        //    difficulties[i] : i번째 문제 난이도
        int[] difficulties = new int[n];
        //    lisEnd[i] : i번째 문제를 끝으로 하는 문제 난이도 순 LIS 길이
        int[] lisEnd = new int[n];
        //    lisStart[i] : i번쩨 문제를 시작으로 하는 문제 난이도 순 LIS 길이
        int[] lisStart = new int[n];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            difficulties[i] = Integer.parseInt(st.nextToken());
            lisEnd[i] = 1;
            lisStart[i] = 1;
        }
        
        for(int i = 1; i < n; i++) {
            for(int j = i - 1; j >= 0; j--) {
                if(difficulties[i] > difficulties[j])
                    lisEnd[i] = Math.max(lisEnd[i], lisEnd[j] + 1);
            }
        }
        
        for(int i = n - 2; i >= 0; i--) {
            for(int j = i + 1; j < n; j++) {
                if(difficulties[i] < difficulties[j])
                    lisStart[i] = Math.max(lisStart[i], lisStart[j] + 1);
            }
        }
        
        for(int i = 0; i < q; i++) {
            int num = Integer.parseInt(br.readLine()) - 1;
            sb.append(lisEnd[num] + lisStart[num] - 1).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```