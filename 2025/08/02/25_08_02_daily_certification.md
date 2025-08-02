# 25_08_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13164 행복 유치원

[13164번: 행복 유치원](http://boj.ma/13164/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    원생 수
    private static int K;    //    그룹 수
    
    private static int cur;
    private static int[] diffArr;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        diffArr = new int[N - 1];
        st = new StringTokenizer(br.readLine());
        
        cur = Integer.parseInt(st.nextToken());
        
        for(int i = 0; i < N - 1; i++) {
            int next = Integer.parseInt(st.nextToken());
            diffArr[i] = next - cur;
            cur = next;
        }
        
        Arrays.sort(diffArr);
        
        for(int i = 0; i < N - K; i++)
            answer += diffArr[i];
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 21758 꿀 따기

[21758번: 꿀 따기](http://boj.ma/21758/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;            //    장소 수 N
    private static int[] honeys;     //    각 장소의 꿀의 양
    
    private static int[] prefixSum;
    private static int[] suffixSum;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        honeys = new int[N + 2];
        prefixSum = new int[N + 2];
        suffixSum = new int[N + 2];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            honeys[i] = Integer.parseInt(st.nextToken());
        
        if(N == 3) {
            System.out.println(Math.max(honeys[1], Math.max(honeys[2], honeys[3])) * 2);
            return;
        }
        
        for(int i = 1; i <= N; i++) {
            prefixSum[i] = prefixSum[i - 1] + honeys[i];
            suffixSum[N - i + 1] = suffixSum[N - i + 2] + honeys[N - i + 1];
        }
        
        for(int i = 2; i <= N - 1; i++) {
            answer = Math.max(answer, prefixSum[N - 1] + prefixSum[i - 1] - honeys[i]);
            answer = Math.max(answer, suffixSum[2] + suffixSum[i + 1] - honeys[i]);
        }
        
        System.out.println(answer);
    }    //    main-ned
}    //    Main-class-end
```