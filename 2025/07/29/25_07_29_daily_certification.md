# 25_07_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15961 회전 초밥

[15961번: 회전 초밥](http://boj.ma/15961/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    회전초밥 개수
    private static int D;    //    회전초밥 종류 (1 ~ D)
    private static int K;    //    연속으로 먹을 회전초밥 개수
    private static int C;    //    K개의 초밥을 연속으로 먹었을때 공짜로 주어지는 초밥 종류 (1 <= C <= D)
    
    private static int[] sushi;        //    i번째 초밥 종류
    private static int[] sushiMap;     //    sushiMap[d] : 연속으로 먹은 K개의 초밥 중 d 종류의 초밥 개수
    private static int sort;           //    연속으로 먹은 K개의 초밥 중 서로 다른 초밥의 종류 수
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        D = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        C = Integer.parseInt(st.nextToken());
        
        sushi = new int[2 * N + 1];
        sushiMap = new int[D + 1];
        sort = 0;
        
        for(int i = 1; i <= N; i++) {
            sushi[i] = Integer.parseInt(br.readLine());
            sushi[i + N] = sushi[i];
            
            if(i <= K) {    //    처음 K개의 초밥을 연속으로 먹는다 가정
                if(sushiMap[sushi[i]] == 0)    //    i번째 초밥 종류는 처음 먹을 경우
                    sort++;                    //    먹은 초밥 종류 수 1 증가
                sushiMap[sushi[i]]++;    //    i번째 초밥 종류 먹은 횟수 증가
            }
        }
        
        answer = sort;
        if(sushiMap[C] == 0)        //    처음 먹은 초밥 K개 중에 C 초밥이 포함되지 않은 경우
            answer++;               //    C 초밥을 공짜로 먹으므로 종류 1개 추가
        
        for(int start = 2; start <= N; start++) {
            int end = start + K - 1;
            
            //    start - 1에 있는 초밥은 먹은 초밥에서 빠짐
            if(sushiMap[sushi[start - 1]] == 1)    //    start - 1에 있는 초밥 종류로 유일했을 경우
                sort--;    //    먹은 초밥 종류 1 감소
            sushiMap[sushi[start - 1]]--;
            //    end에 있는 초밥은 먹은 초밥에 추가됨
            if(sushiMap[sushi[end]] == 0)    //    end에 있는 초밥 종류는 먹은 적이 없을 경우
                sort++;    //    먹은 초밥 종류 1 증가
            sushiMap[sushi[end]]++;
            
            answer = Math.max(answer, sort);
            if(sushiMap[C] == 0)
                answer = Math.max(answer, sort + 1);
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 15817 배수 공사

[15817번: 배수 공사](http://boj.ma/15817/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    파이프 종류 수
    private static int X;    //    만들고자 하는 파이프 길이
    
    private static int[] lenArr;    //    lenArr[i] : i번째 파이프 길이
    private static int[] cntArr;    //    cntArr[i] : i번째 파이프 개수
    
    private static int[] dp;    //    dp[x] : 길이 x인 파이프를 만드는 경우의 수 
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        X = Integer.parseInt(st.nextToken());
        
        lenArr = new int[N + 1];
        cntArr = new int[N + 1];
        dp = new int[X + 1];
        dp[0] = 1;
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            lenArr[i] = Integer.parseInt(st.nextToken());
            cntArr[i] = Integer.parseInt(st.nextToken());
        }
        
        for(int i = 1; i <= N; i++) {
            int len = lenArr[i];
            int cnt = cntArr[i];
            
            for(int x = X; x >= 1; x--) {
                for(int j = 1; j <= cnt; j++) {
                    if(x - len * j >= 0)
                        dp[x] += dp[x - len * j];
                }
            }
        }
        
        System.out.println(dp[X]);
    }    //    main-end
}    //    Main-class-end
```