# 25_08_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1700 멀티탭 스케줄링

[1700번: 멀티탭 스케줄링](http://boj.ma/1700/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final int NONE = Integer.MAX_VALUE;    //    이후로 더 이상 사용되지 않음
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    멀티탭 구멍 개수
    private static int K;    //    전기용품 개수/총 사용 횟수
    
    private static int[] sequence;        //    전기용품 사용 순서
    private static int[][] nextUsed;      //    nextUsed[k][s] : k번째 전기용품이 s번째 이후 순서 중 가장 빠르게 사용될 순서, NONE일 경우, 더 이상 사용되지 않음
    private static boolean[] used;        //    used[k] : k번째 전기용품이 지금 멀티탭에 꽂혀 있으면 true
    private static int remain = 0;        //    남은 멀티탭 구멍 개수
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        sequence = new int[K + 1];
        nextUsed = new int[K + 1][K + 1];
        used = new boolean[K + 1];
        for(int k = 0; k <= K; k++)
            Arrays.fill(nextUsed[k], NONE);
        remain = N;
        
        st = new StringTokenizer(br.readLine());
        for(int s = 1; s <= K; s++)
            sequence[s] = Integer.parseInt(st.nextToken());
        
        for(int k = 1; k <= K; k++) {
            int lastUsed = sequence[K] == k ? K : NONE;
            
            for(int s = K - 1; s >= 1; s--) {
                nextUsed[k][s] = lastUsed;
                
                if(sequence[s] == k)
                    lastUsed = s;
            }
        }

        for(int s = 1; s <= K; s++) {
            int cur = sequence[s];    //    이번 순서에 사용될 전기용품
            
            if(used[cur])    //    이미 멀티탭에 꽂혀있을 경우
                continue;
            else {    //    멀티탭에 꽂아야 할 경우
                if(remain > 0)      //    아직 멀티탭에 구멍이 남아 있을 경우
                    remain--;       //    구멍 1개 감소
                else {    //    멀티탭에 구멍이 남아 있지 않아서 다른 전기 용품 하나를 뽑아야 할 경우
                    int lastUsedS = -1;    //    가장 나중에 사용되는 순서
                    int lastUsedK = -1;    //    가장 나중에 사용되는 전기용품
                    
                    for(int k = 1; k <= K; k++) {
                        if(used[k]) {    //    이미 사용 중인 전기용품
                            int nextUsedS = nextUsed[k][s];
                            
                            if(lastUsedS < nextUsedS) {
                                lastUsedS = nextUsedS;
                                lastUsedK = k;
                            }
                        }
                    }
                    
                    used[lastUsedK] = false;
                    answer++;    //    멀티탭에서 하나 뽑기    
                }
                
                used[cur] = true;    //    멀티탭에 꽂기
            } 
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```